# Create API Endpoint Examples

## Example: CRUD for comments nested under posts

**Input**: `CRUD endpoints for a "comments" resource nested under posts`

### Route handler

```ts
// src/api/handlers/comments.ts
import { Request, Response } from "express";
import { commentService } from "../../services/CommentService";
import {
  CreateCommentParamsSchema,
  CreateCommentBodySchema,
  ListCommentsQuerySchema,
} from "../schemas/comments";

export async function createComment(req: Request, res: Response) {
  const { postId } = CreateCommentParamsSchema.parse(req.params);
  const body = CreateCommentBodySchema.parse(req.body);

  const comment = await commentService.create(postId, body);
  return res.status(201).json({ data: comment });
}

export async function listComments(req: Request, res: Response) {
  const { postId } = CreateCommentParamsSchema.parse(req.params);
  const { page, limit } = ListCommentsQuerySchema.parse(req.query);

  const { comments, total } = await commentService.listByPost(postId, { page, limit });
  return res.json({
    data: comments,
    meta: { page, limit, total },
  });
}
```

### Validation schema

```ts
// src/api/schemas/comments.ts
import { z } from "zod";

export const CreateCommentParamsSchema = z.object({
  postId: z.string().cuid(),
});

export const CreateCommentBodySchema = z.object({
  content: z.string().min(1).max(5000),
  authorId: z.string().cuid(),
});

export const ListCommentsQuerySchema = z.object({
  page: z.coerce.number().int().positive().default(1),
  limit: z.coerce.number().int().min(1).max(100).default(20),
});
```

### Service layer

```ts
// src/services/CommentService.ts
import { prisma } from "../db";

interface CreateCommentInput {
  content: string;
  authorId: string;
}

export const commentService = {
  async create(postId: string, input: CreateCommentInput) {
    return prisma.comment.create({
      data: { ...input, postId },
      select: { id: true, content: true, authorId: true, createdAt: true },
    });
  },

  async listByPost(postId: string, { page, limit }: { page: number; limit: number }) {
    const [comments, total] = await prisma.$transaction([
      prisma.comment.findMany({
        where: { postId },
        select: { id: true, content: true, authorId: true, createdAt: true },
        skip: (page - 1) * limit,
        take: limit,
        orderBy: { createdAt: "desc" },
      }),
      prisma.comment.count({ where: { postId } }),
    ]);
    return { comments, total };
  },
};
```
