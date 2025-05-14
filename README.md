# Supabase CRUD App with React Router V7

<div align="center">
  <br />
  <a href="https://youtu.be/your-video-id" target="_blank">
    <img src="./banner.png" alt="Project Banner">
  </a>
  <br />
  <div>
    <img src="https://img.shields.io/badge/-React-61DAFB?style=for-the-badge&logo=react&logoColor=black" alt="React" />
    <img src="https://img.shields.io/badge/-TailwindCSS-06B6D4?style=for-the-badge&logo=tailwindcss" alt="Tailwind CSS" />
    <img src="https://img.shields.io/badge/-Vite-646CFF?style=for-the-badge&logo=vite&logoColor=white" alt="Vite" />
    <img src="https://img.shields.io/badge/-TypeScript-3178C6?style=for-the-badge&logo=typescript" alt="TypeScript" />
    <img src="https://img.shields.io/badge/-Supabase-FB5A41?style=for-the-badge&logo=supabase" alt="Supabase" />
  </div>
  <h3 align="center">Build a Robust CRUD App with React Router V7 & Supabase</h3>
  <div align="center">
    Follow along with our detailed tutorial on 
    <a href="https://youtu.be/waI5CDisiuM" target="_blank"><b>YouTube</b></a>
  </div>
  <br />
</div>

## 📋 Table of Contents

1. [Introduction](#introduction)
2. [Tech Stack](#tech-stack)
3. [Features](#features)
4. [Quick Start](#quick-start)
5. [Code Snippets](#code-snippets)
6. [Assets & More](#assets--more)

## 🚀 Introduction

In this video tutorial, you'll learn how to build a robust CRUD application using React Router V7 and Supabase. We demonstrate how to create, read, update, and delete items stored in Supabase using React Router V7’s new configuration-based routing and module features. The project utilizes TypeScript for strong typing and Tailwind CSS for a beautiful, responsive UI with gradient-styled item cards. Watch the full tutorial on [YouTube](https://youtu.be/your-video-id) for a step-by-step guide.

## ⚙️ Tech Stack

- **React** – For building the user interface
- **Vite** – For fast development and optimized builds
- **Tailwind CSS** – For rapid, responsive styling with utility classes
- **TypeScript** – For type safety and modern JavaScript features
- **Supabase** – For real-time database, authentication, and storage
- **React Router V7** – For declarative, configuration-based routing

## ⚡️ Features

- **CRUD Operations:**  
  Seamlessly create, read, update, and delete items stored in Supabase.
  
- **Dynamic Routing:**  
  Utilize React Router V7's modular route configuration to build scalable, nested routes.

- **Data Loading & Actions:**  
  Use loaders and actions to fetch data from Supabase and manage form submissions.

- **Modern UI:**  
  Enjoy a polished and responsive interface styled with Tailwind CSS, featuring gradient item cards and centered layout.

- **Type Safety:**  
  Benefit from TypeScript's robust type system during development.

## 👌 Quick Start

### Prerequisites

- [Git](https://git-scm.com/)
- [Node.js](https://nodejs.org/en/)
- [npm](https://www.npmjs.com/)
- A [Supabase](https://supabase.com/) account with a project and table setup

### Cloning the Repository

bash
git clone https://github.com/yourusername/rrv7-crud.git
cd rrv7-crud


### Installing Dependencies

bash
npm install


### Running the Development Server

bash
npm run dev


Your app will be running at [http://localhost:5173](http://localhost:5173).

## 💻 Code Snippets

### Sample Supabase Table Creation

sql
CREATE TABLE public.items (
  id SERIAL PRIMARY KEY,
  title TEXT NOT NULL,
  content TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);


### Items Route Module

tsx
// src/app/routes/items.tsx
import React, { useState } from "react";
import { Form, Link, redirect } from "react-router";
import { supabase } from "../../supabaseClient";

export function meta() {
  return [
    { title: "Items | SupaCRUD" },
    { name: "description", content: "Manage your items in the Supabase CRUD app." },
  ];
}

export async function loader() {
  const { data, error } = await supabase
    .from("items")
    .select("*")
    .order("id", { ascending: false });
  if (error) throw new Response(error.message, { status: 500 });
  return data;
}

export async function action({ request }: { request: Request }) {
  const formData = await request.formData();
  const title = formData.get("title") as string;
  const content = formData.get("content") as string;
  if (!title || !content) return { error: "Title and content are required" };

  const { error } = await supabase.from("items").insert([{ title, content }]);
  if (error) return { error: error.message };
  return redirect("/");
}

export default function Items({ loaderData, actionData }: any) {
  const items = loaderData;
  const [isCreating, setIsCreating] = useState(false);

  return (
    <div className="flex flex-col items-center">
      <h2 className="text-3xl font-bold text-gray-800 mb-6">Items</h2>
      {actionData?.error && (
        <div className="bg-red-200 text-red-800 p-2 mb-4 rounded">
          {actionData.error}
        </div>
      )}
      <div className="mb-8 w-full max-w-md">
        <button
          onClick={() => setIsCreating(!isCreating)}
          className="w-full mb-2 bg-indigo-600 text-white px-4 py-2 rounded hover:bg-indigo-500"
        >
          {isCreating ? "Cancel" : "New Item"}
        </button>
        {isCreating && (
          <Form method="post" className="space-y-4 bg-white p-4 rounded shadow">
            <div>
              <label className="block text-gray-700">Title</label>
              <input name="title" type="text" className="border border-gray-300 rounded px-3 py-2 w-full" required />
            </div>
            <div>
              <label className="block text-gray-700">Content</label>
              <textarea name="content" className="border border-gray-300 rounded px-3 py-2 w-full" required></textarea>
            </div>
            <button type="submit" className="w-full bg-green-600 text-white px-4 py-2 rounded hover:bg-green-500">
              Create Item
            </button>
          </Form>
        )}
      </div>
      <ul className="grid grid-cols-1 gap-6 sm:grid-cols-2 md:grid-cols-3 w-full max-w-4xl">
        {items?.map((item: any) => (
          <li
            key={item.id}
            className="p-6 rounded-lg shadow-lg bg-gradient-to-r from-indigo-500 to-purple-500 text-white text-center"
          >
            <Link to={`/items/${item.id}`} className="block text-2xl font-bold hover:underline mb-2">
              {item.title}
            </Link>
            <p className="text-lg">{item.content}</p>
          </li>
        ))}
      </ul>
    </div>
  );
}


### Item Edit Module

tsx
// src/app/routes/item.tsx
import React from "react";
import { Form, redirect, useNavigate } from "react-router";
import { supabase } from "../../supabaseClient";

export function meta({ params }: { params: { id: string } }) {
  return [
    { title: `Edit Item ${params.id} | SupaCRUD` },
    { name: "description", content: "Edit or delete an item in the Supabase CRUD app." },
  ];
}

export async function loader({ params }: { params: { id: string } }) {
  const { id } = params;
  if (!id) throw new Response("Item ID is required", { status: 400 });
  const { data, error } = await supabase.from("items").select("*").eq("id", id).single();
  if (error) throw new Response(error.message, { status: 500 });
  return data;
}

export async function action({ request, params }: { request: Request, params: { id: string } }) {
  const formData = await request.formData();
  const intent = formData.get("intent");
  const { id } = params;
  if (!id) throw new Response("Item ID is required", { status: 400 });

  if (intent === "delete") {
    const { error } = await supabase.from("items").delete().eq("id", id);
    if (error) return { error: error.message };
    return redirect("/");
  } else if (intent === "update") {
    const title = formData.get("title") as string;
    const content = formData.get("content") as string;
    const { error } = await supabase.from("items").update({ title, content }).eq("id", id);
    if (error) return { error: error.message };
    return { updated: true };
  }
  return {};
}

export default function Item({ loaderData, actionData }: any) {
  const item = loaderData;
  const navigate = useNavigate();

  return (
    <div className="max-w-md mx-auto">
      <h2 className="text-2xl font-bold text-gray-800 mb-4">Edit Item</h2>
      {actionData?.error && (
        <div className="bg-red-200 text-red-800 p-2 mb-4 rounded">
          {actionData.error}
        </div>
      )}
      {actionData?.updated && (
        <div className="bg-green-200 text-green-800 p-2 mb-4 rounded">
          Item updated successfully!
        </div>
      )}
      <Form method="post" className="space-y-4 bg-white p-4 rounded shadow">
        <div>
          <label className="block text-gray-700">Title</label>
          <input name="title" type="text" defaultValue={item.title} className="border border-gray-300 rounded px-3 py-2 w-full" required />
        </div>
        <div>
          <label className="block text-gray-700">Content</label>
          <textarea name="content" defaultValue={item.content} className="border border-gray-300 rounded px-3 py-2 w-full" required></textarea>
        </div>
        <div className="flex space-x-4">
          <button type="submit" name="intent" value="update" className="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-500">
            Update
          </button>
          <button type="submit" name="intent" value="delete" className="bg-red-600 text-white px-4 py-2 rounded hover:bg-red-500">
            Delete
          </button>
        </div>
      </Form>
    </div>
  );
}


Feel free to customize this README further to better match your project and presentation. Happy coding and enjoy building your Supabase CRUD app with React Router V7!
