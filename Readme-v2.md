# Sign in, sign up and Sign out: 

```tsx
import { SignInButton, SignOutButton, SignUpButton } from '@clerk/nextjs'
import React from 'react'

export default function Navbar() {
    return (
        <nav className="flex justify-end items-center p-4 gap-4 h-16">
            {/* default mode is redirect */}
            <SignInButton mode='modal'></SignInButton>
            <SignUpButton></SignUpButton>
            <SignOutButton></SignOutButton>
        </nav>
    )
}
```

# user button (for signout, manage account which contains profile and security)

```tsx
import { SignInButton, SignOutButton, SignUpButton, UserButton } from '@clerk/nextjs'
import React from 'react'

export default function Navbar() {
    return (
        <nav className="flex justify-end items-center p-4 gap-4 h-16">
            {/* default mode is redirect */}
            <SignInButton mode='modal'></SignInButton>
            <SignUpButton></SignUpButton>
            <SignOutButton></SignOutButton>
            <UserButton></UserButton>
        </nav>
    )
}
```

What if we want the mange account section within a separated routes: 

```tsx
import { SignInButton, SignOutButton, SignUpButton, UserButton } from '@clerk/nextjs'
import Link from 'next/link'
import React from 'react'

export default function Navbar() {
    return (
        <nav className="flex justify-end items-center p-4 gap-4 h-16">
            {/* default mode is redirect */}
            <SignInButton mode='modal'></SignInButton>
            <SignUpButton></SignUpButton>
            <SignOutButton></SignOutButton>
            <UserButton></UserButton>
            <Link href={'/user-profile'}>Profile</Link>
        </nav>
    )
}
```

```tsx
import { UserProfile } from '@clerk/nextjs'
import React from 'react'

export default function UserProfilePage() {
    return (
        <div className='flex justify-center mb-5'>
            <UserProfile path='/user-profile'></UserProfile>
        </div>
    )
}
```

# Conditional UI Rendering: 

```tsx
import { Show, SignInButton, SignOutButton, SignUpButton, UserButton } from '@clerk/nextjs'
import Link from 'next/link'
import React from 'react'

export default function Navbar() {
    return (
        <nav className="flex justify-end items-center p-4 gap-4 h-16">
            {/* default mode is redirect */}
            <Show when={'signed-out'}>
                <SignInButton mode='modal'></SignInButton>
                <SignUpButton></SignUpButton>
            </Show>

            <Show when={'signed-in'}>
                <UserButton></UserButton>
                <Link href={'/user-profile'}>Profile</Link>
                <SignOutButton></SignOutButton>
            </Show>
        </nav>
    )
}
```

# Protecting Routes: 

```tsx
import { clerkMiddleware, createRouteMatcher } from '@clerk/nextjs/server';



const isProtectedRoutes = createRouteMatcher(["/user-profile"])

export default clerkMiddleware(async (auth, req) => {
    if (isProtectedRoutes(req)) {
        await auth.protect()
    }
});

export const config = {
    matcher: [
        // Skip Next.js internals and all static files, unless found in search params
        '/((?!_next|[^?]*\\.(?:html?|css|js(?!on)|jpe?g|webp|png|gif|svg|ttf|woff2?|ico|csv|docx?|xlsx?|zip|webmanifest)).*)',
        // Always run for API routes
        '/(api|trpc)(.*)',
    ],
};
```

or we can create public routes instead of protected routes: 

```tsx
import { clerkMiddleware, createRouteMatcher } from '@clerk/nextjs/server';


const isPublicRoute = createRouteMatcher(["/", "/sign-in(.*)", "/sign-up(.*)"])

export default clerkMiddleware(async (auth, req) => {
    if (!isPublicRoute(req)) {
        await auth.protect()
    }
});

export const config = {
    matcher: [
        // Skip Next.js internals and all static files, unless found in search params
        '/((?!_next|[^?]*\\.(?:html?|css|js(?!on)|jpe?g|webp|png|gif|svg|ttf|woff2?|ico|csv|docx?|xlsx?|zip|webmanifest)).*)',
        // Always run for API routes
        '/(api|trpc)(.*)',
    ],
};
```

Or

```tsx
import { clerkMiddleware, createRouteMatcher } from '@clerk/nextjs/server';


const isPublicRoute = createRouteMatcher(["/", "/sign-in(.*)", "/sign-up(.*)"])

export default clerkMiddleware(async (auth, req) => {
    // if (!isPublicRoute(req)) {
    //     await auth.protect()
    // }

    const { userId, redirectToSignIn } = await auth()

    if (!userId && !isPublicRoute) {

        //   add custom logic to run here before redirecting 

        return redirectToSignIn
    }

});

export const config = {
    matcher: [
        // Skip Next.js internals and all static files, unless found in search params
        '/((?!_next|[^?]*\\.(?:html?|css|js(?!on)|jpe?g|webp|png|gif|svg|ttf|woff2?|ico|csv|docx?|xlsx?|zip|webmanifest)).*)',
        // Always run for API routes
        '/(api|trpc)(.*)',
    ],
};
```

# Read Session and User Data: 

```tsx
import { auth, currentUser } from '@clerk/nextjs/server'
import React from 'react'

export default async function DashboardHomePage() {

    const authObj = await auth()
    const userObj = await currentUser()

    console.log("authObj", authObj)
    console.log("userObj", userObj)

    return (
        <div>DashboardHomePage</div>
    )
}
```

Note: for client component use `useAuth` and `useUser`
