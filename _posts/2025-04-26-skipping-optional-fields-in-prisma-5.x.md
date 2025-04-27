---           
layout: post
title: Skipping Optional Fields in Prisma 5.x
date: 2025-04-26 00:00:00 IST
comments: true
categories: typescript orm prisma
---

## Introduction

I use Prisma ORM for database access in my TypeScript projects. I recently ran into an issue where I needed to skip optional fields in an upsert query.
Setting undefined is [not allowed](https://www.prisma.io/docs/orm/prisma-client/special-fields-and-types/null-and-undefined) in Prisma to avoid
 unexpected results. The prescribed solution - to set Prisma.skip - does not work in the version I'm using. Upgrading Prisma was not an option as I was in the middle of a feature. 
 
So here's what worked for me. 

**tldr;** I wrote a function that creates args objects which have only the fields that are present in the incoming data object.

### The schema
```typescript
model PSP {
  id           Int      @id @default(autoincrement())
  userId       String
  pageId       String   @unique
  pageTitle    String
  companyURL   String?
  supportEmail String?
}
```

The last 3 fields are optional.

### The query
A Prisma query for upserting in the case where none of the fields are optional would look like:

```typescript

    const pspData = ....//Data object with values from the user
    ....

    const psp = await db.pSP.upsert({
        where: {
            pageId: update.pageId,
            userId: update.userId
        },
        create: {
            pageTitle: pspData.pageTitle,
            companyURL: pspData.companyURL,//Optional
            supportEmail: pspData.supportEmail,//Optional
        },
        update: {
            pageTitle: pspData.pageTitle,
            companyURL: pspData.companyURL,//Optional
            supportEmail: pspData.supportEmail,//Optional
        }
    });
```

Obviously, this does not work as the optional fields would be undefined, and Prisma does not allow that. 

The first approach is a brute force way where I created the args objects based on which fields were present in the incoming data object.

```typescript
const getCreateUpdateArgs = (update: PSPUpdate): { createArgs: any, updateArgs: any } => {
    const createArgs: any = {}
    const updateArgs: any = {}
    if (update.pageTitle) {
        createArgs.pageTitle = update.pageTitle;
        updateArgs.pageTitle = update.pageTitle;
    }
    if (update.companyURL) {
        createArgs.companyURL = update.companyURL;
        updateArgs.companyURL = update.companyURL;
    }
    if (update.supportEmail) {
        createArgs.supportEmail = update.supportEmail;
        updateArgs.supportEmail = update.supportEmail;
    }
    createArgs.pageId = update.pageId;
    createArgs.userId = update.userId;

    return { createArgs, updateArgs };
}
```

I then used the function's return values in the upsert query.

```typescript
    const { createArgs, updateArgs } = getCreateUpdateArgs(pspData);

    const psp = await db.pSP.upsert({
        where: {
            pageId: update.pageId,
            userId: update.userId
        },
        create: {
            ...createArgs
        },
        update: {
            ...updateArgs
        }
    });
```

This works, but I did not like the idea of checking each field in the incoming data object. My JavaScript skills are not great, so after a bit of searching this is what I 
came up with:

```typescript

const getCreateUpdateArgs2 = (update: PublicStatusPageUpdate): { createArgs: any, updateArgs: any } => {
    const createArgs: any = {}
    const updateArgs: any = {}

    if (typeof update !== 'object' || update === null) {
        throw new Error("Invalid update object");//Unexpected
    }

    if (Object.getOwnPropertyNames(update).length === 0) {
        return { createArgs, updateArgs };
    }

    Object.entries(update).forEach(([key, value]) => {
        if (value && !immutables.includes(key)) {
            updateArgs[key] = value;
            createArgs[key] = value;
        }
    });

    return { createArgs, updateArgs };
}

```

This assumes a few things: 
- That there are no nested objects.
- There are no fields we want to exclude from the update.

I'm sure there are edge cases I'm not considering, but it works for now.