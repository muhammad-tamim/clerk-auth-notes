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
