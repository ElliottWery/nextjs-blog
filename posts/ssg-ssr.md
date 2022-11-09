---
title: 'When to Use Static Generation v.s. Server-side Rendering'
date: '2020-01-02'
---

We recommend using **Static Generation** (with and without data) whenever possible because your page can be built once and served by CDN, which makes it much faster than having a server render the page on every request.

You can use Static Generation for many types of pages, including:

- Marketing pages
- Blog posts
- E-commerce product listings
- Help and documentation

You should ask yourself: "Can I pre-render this page **ahead** of a user's request?" If the answer is yes, then you should choose Static Generation.

On the other hand, Static Generation is **not** a good idea if you cannot pre-render a page ahead of a user's request. Maybe your page shows frequently updated data, and the page content changes on every request.

In that case, you can use **Server-Side Rendering**. It will be slower, but the pre-rendered page will always be up-to-date. Or you can skip pre-rendering and use client-side JavaScript to populate data.











 EXAMPLE USING SSG

 // pages/blog/[slug].js

import Head from 'next/head';

export async function getStaticPaths() {
  // Call an external API endpoint to get posts
  const res = await fetch('https://www.example.com/api/posts');
  const posts = await res.json();

  // Get the paths we want to pre-render based on posts
  const paths = posts.map((post) => ({
    params: { slug: post.slug },
  }));
  // Set fallback to blocking. Now any new post added post build will SSR
  // to ensure SEO. It will then be static for all subsequent requests
  return { paths, fallback: 'blocking' };
}

export async function getStaticProps({ params }) {
  const res = await fetch(`https://www.example.com/api/blog/${params.slug}`);
  const data = await res.json();

  return {
    props: {
      blog: data,
    },
  };
}

function BlogPost({ blog }) {
  return (
    <>
      <Head>
        <title>{blog.title} | My Site</title>
      </Head>
      <div>
        <h1>{blog.title}</h1>
        <p>{blog.text}</p>
      </div>
    </>
  );
}

export default BlogPost;





EXAMPLE USING SSR 

// pages/blog/[slug].js

import Head from 'next/head';
function BlogPost({ blog }) {
  return (
    <div>
      <Head>
        <title>{blog.title} | My Site</title>
      </Head>
      <div>
        <h1>{blog.title}</h1>
        <p>{blog.text}</p>
      </div>
    </div>
  );
}

export async function getServerSideProps({ query }) {
  const res = await fetch(`https://www.example.com/api/blog/${query.slug}`);
  const data = await res.json();

  return {
    props: {
      blog: data,
    },
  };
}

export default BlogPost;