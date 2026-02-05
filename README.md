---
marp: true
theme: gaia
class: invert
paginate: true
style: |
  @import url('https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;700&family=Roboto:wght@400;700&display=swap');

  section {
    font-family: 'Roboto', sans-serif;
    background-color: #1e1e1e;
    color: #e0e0e0;
    font-size: 26px;
  }

  /* Tech Headlines */
  h1, h2, h3 {
    font-family: 'Roboto', sans-serif;
    color: #38bdf8;
    text-transform: uppercase;
    letter-spacing: 1px;
  }
  
  h1 { 
    font-size: 3.5em; 
    text-shadow: 0 0 20px rgba(56, 189, 248, 0.4);
  }

  blockquote {
    background: #2d2d2d;
    border-left: 5px solid #38bdf8;
    padding: 20px;
    border-radius: 0 8px 8px 0;
  }

  .title-info {
    padding-left:10px;
    display: inline-block;
    text-align: left;
    margin-top: 1rem;
    font-family: 'JetBrains Mono', monospace;
  }

  .subtitle {
    display: block;
    font-size: 0.6em;
    color: #94a3b8;
    text-transform: none;
  }

  img { border: 2px solid #38bdf8; border-radius: 8px; }

---

# LAB Spatie RBAC

<div class="title-info">

**Dev:** Ben Taleb Mehdi
**Lead:** Mr.ESSARRAJ Fouad
**Lab:** Laravel Permissions
**Date:** 05/01/2026

</div>

---

## Sommaire

1. **The Architecture**
   <span class="subtitle">The Polymorphic Database Schema</span>

2. **Integration (DNA)**
   <span class="subtitle">Traits, Seeders, and Model Configuration</span>

3. **Protection (Gates)**
   <span class="subtitle">Middleware & Super-Admin Bypassing</span>

4. **The Frontend (UI)**
   <span class="subtitle">Blade Directives & Dynamic Visibility</span>

---

## 00. The Core Concept

> Decoupling Authorization from Application Logic.

* **Identity:** Moving away from static checks like `is_admin`.
* **Roles:** Groups of permissions (e.g., `Super-Admin`, `Editor`).
* **Permissions:** Atomic actions (e.g., `edit-posts`, `delete-users`).
* **Spatie Engine:** Provides a robust API to manage these relationships dynamically via the database.

---

## 01. Architecture (The Schema)

The package adds a specialized relational structure to your Laravel app.



* **Dynamic Mapping:** Links Users to Roles and Roles to Permissions.
* **Polymorphic:** Can be attached to any model (not just Users).
* **Cache-Driven:** Uses Laravel Cache to prevent redundant DB queries during authorization checks.

---

## 02. Implementation (DNA)

Active "Playbooks" for setting up the security layer.

* **The Trait:** Add `use HasRoles;` to the User model.
* **The Seeder:** Automated setup of the security hierarchy.
    ```php
    $role = Role::create(['name' => 'writer']);
    $role->givePermissionTo('edit-articles');
    ```
* **Persistence:** Roles and permissions are persisted in the database, allowing for runtime changes without code deployments.

---

## 03. Protection (Workflows)

How the system enforces security constraints.

* **Middleware:** Secure routes instantly.
    * `Route::middleware(['role:admin'])`
* **Gate Interception:** The **Super-Admin** rule.
    ```php
    Gate::before(function ($user, $ability) {
        return $user->hasRole('Super-Admin') ? true : null;
    });
    ```
* **Self-Correction:** If a user lacks a role, the system triggers a 403 Unauthorized response automatically.

---

## 04. Frontend (Modular Skills)

UI logic that adapts to user capabilities.

* **On-Demand Visibility:** Loading buttons only for those who can use them.
* **Blade Directives:**
    * `@can('publish')` -> Shows "Publish" button.
    * `@role('admin')` -> Shows "Admin Panel" link.
* **Context Efficiency:** Prevents "dead clicks" by hiding restricted actions before the user interacts with them.

