---
layout: post
title: Nextjs Key Feature Summary
subtitle: The React Framework for Production
date: 2022-06-14
author: Shawn Wong
image: xxx.png
excerpt: Nextjs allows us to determine WHEN a page should be pre-rendered.
isFeatured: true
catalog: true
tags:
  - Nextjs
  - SSG (Static Site Generation)
  - SSR (Server Side Render)
  - BSR (Browser/Client Side Render) => React
---

## Pages & File-based Routings (Client Side)

1. [id] can be used in receive customized router

2. [id] --> [clientprojectid] nested dynamic folder  
   when we use router.query => {id:"aaa", clientProjectid: "bbb"}

3. [...slug] can receive as many folders with router.query and show results as array["a", "b", "c"] (when we use router.query)

eg. file path: current path page/portfolio/[projectid].js  
now, try to access domain-name/portfolio/sth

```js
import { useRouter } from 'next/router';
const router = useRouter();
router.pathname; // portfolio/[projectid]
router.query; // {projectid : sth}
```

## Pages & File-based Routings: Link

1. Link preload related resources

2. other Link props:

<Link href={{pathname: "/clients/[id]", query: {id: XXX}}}>

<Link href={{pathname: "/clients/[id]/[clientprojectid]", query: {id: XXX, clientprojectid: XXX}}}>

or

<Link href={`/cc/${x.id}`}>

3. const router = useRouter()  
   router.push('/') // redirect pages  
   router.replace('/') // redirect pages and cannot go back

## JS File Reading

```js
// stands for current working directory(in the root directory)
// find data folder, find dummy-backend.json file
const filePath = path.join(process.cwd(), 'data', 'dummy-backend.json' );

import fs from 'fs' or import fs from 'fs/promise'

fs.readFileSync() // sync the read file and block the execution until its done

fs.readFile() // callback function
```

## Page Pre-Rendering & Data Fetching: SSG

### How to regenerate pages periodically by SSG situation?

```js
return {

     props:  {

          products: data.products

      },

      revalidate: 10,  // refresh pages each 10 seconds in production env. it will regenerate all the times in dev env

      notFound: true, // show 404 page. (when their is no data),

      redirect: {

             destination: '/no-data' // redirect page for routering

}

```

### Working With Dynamic Parameters: SSG

```js
getStaticProps(context){

     const {params} = context;
    // path: page/[pid]
     const productId = params.pid; // get all URL path through context, not router(router is for browser)

}

getStaticPaths(){

    return {

        // pre generate dynamic pages for pre-render, because we use getStaticProps for SSG

         paths: [{params: {pid: 'p1'}}...] ,

        // fallback == true: loading p2, p3 on the real time(wont be appear in .next folder, not pre generated), need to be loading.... eg. paths:[] that could be fine for lots of contents

        // fallback == true + params
        // in this situation, page is not accessble directly with router path.(eg. /p4) need to use loading... to wait loading data
        //  ==> {notFound: true}
        // fallback == 'blocking': show pages until full loading...
         fallback: false // pregenerated all the listed pages
    }
}
```

### Summary SSG, SSR

next fetching data:

1. getStaticProps: pre-render in server (SSG)

2. getStaticProps + getStaticPaths: dynamic pre-render in server (SSG+SSR with fallback parameter)

3. getServerSideProps: purely server-side render. no need to add getStaticPaths

4. getStaticProps + useEffect[data] + useState: pre-render + dynamic data fetching (swr for setTimeout)

## Optimizing Nextjs Apps

```js
import Head from 'next/head'
<Head>
  <meta name='viewport' content="initial-scale=1.0, width="device-width">
</Head>

// this one can be put in any pages
<Head>
  <title>React Meetups</title> // <title>{props.meetupData.title}</title>
  <meta name="description" content="Browse a huge list of highly active..." />
</Head>
```

Head can also be set in \_app.js which can be overridden by the details in the children's components.  
Head can also be set into

## Lazy Loading Images

```js
import Image from 'next/image'

<Image src={`/${image}`} alt={XXX} width={160} height={160}>  // 160px

benefits:

1. lazy loading when you need that images

2. modify size and types to be suitable for the current web size

https://nextjs.org/docs/api-reference/next/image
```

## \_documents config

\_document.js: allow to add config for all pages

```js
import Document, { Html, Head, Main, NextScript } from 'next/document';

class MyDocument extends Document {
  render() {
    return (
      <Html>
        <Head />
        <body>
          <Main />
          <NextScript />
        </body>
      </Html>
    );
  }
}

export default Document;
```

## Adding Backend Code with API Routes

```js
req.body; // for accessing submitted data

req.method; // get what kind of query you get eg. get/post

req.query; // for query parameters
```

## Deloying Nextjs Apps

for the Next export:

there is no server-side code, API, page revalidations, no fallbacks, no server-side props, no image optimization(render on-demand, on the fly)

or we can use build to package files and deploy it in S3

## Next Auth

next-auth use cookie and session to handle login and logout automatically.

### The difference between useSession and getSession?

useSession: ReactJS hooks only worked on client side. The return from it help to update UI state, whichi is made above getSession.  
getSession: thenable function promise. Read current cookie and return session, which is suitable for both client side and server side.

In server side:

```js
import { getSession } from 'next-auth/client';

export async function getServerSideProps(context) {
  const session = await getSession({ req: context.req });
  // if it didnt login, it goes to login page
  if (!session) {
    return {
      redirect: {
        destination: '/auth',
        permanent: false,
      },
    };
  }
  return {
    // otherwise, return logined session
    props: { session },
  };
}
```

In client side:

```js
import { useSession, signOut } from 'next-auth/client';
function MainNavigation() {
  const [session, loading] = useSession();
  return !session && !loading && XXXXX;
}
```

### Native DOM operations for router changes

```js
window.location.href = '/auth';
```

### api folder setting up Provider.Credentials

build a new [...nextauth].ts file under api folder

```js
import NextAuth from 'next-auth';
import Providers from 'next-auth/providers';
export default NextAuth({
  session: {
    jwt: true,
  },
  providers: [
    Providers.Credentials({
      async authorize(credentials: IAuth) {
        // connect to database

        // get this user info from database

        // return user info as token
        return {
          email: user.email,
        };
      },
    }),
  ],
});
```
