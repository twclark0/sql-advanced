# Advanced SQL

✌️Hello! My name is Tyler Clark and this is the
source material for my Advanced SQL workshop

## Why Should You Care About this Workshop?

Let's face it, data powers the web. And I am not only talking about your twitter posts you see in your feed. I'm talking about analytics, artificial intelligence, and even the weather is literally powered by data. Also, look at the rise in python over the last decade. It's recently overtaken JavaScript as the most popular programming language. Why you might ask? To handle complex data. This course is designed to take your basic sql CRUD (create, replace, update, delete) to the next level by learning more in-depth concepts.

Even if you classify yourself as a frontend developer, I promise you this course will help you out in some point of your career. We become better frontend developers by learning more about how data is constructed and queried.

---

## Pre-Workshop Instructions/Requirements

This workshop is helpful for developers that have basic knowledge of SQL.

- [ ] Setup the project (follow the setup instructions below)
- [ ] Install and setup [Zoom](https://zoom.us) on the computer you will be using

## Workshop Outline

While there are similarities between each lesson, we will work through each lesson mostly independently of each other.

1. Bulk insert / update / export
2. The insert on conflict flag
3. Casting types
4. Defining custom types
5. Query performance tuning. Understanding the query planner by using `explain` and `analyze`
6. Creating Common Table Expressions (CTE)
7. Filter aggregated data with `having`
8. Defining scopes and variables with `do` and `declare`
9. Conditional returns with `case`, `when`, `then`, `else`, & `end`
10. Perform Multiple Steps in One with Transactions

## System Requirements

1. [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) v2 or greater

Git must be available in your `PATH`. To verify things are set up
properly, you can run this:

```shell
git --version
```

If you have trouble with any of these, learn more about the PATH environment
variable and how to fix it here for [windows][win-path] or
[mac/linux][mac-path]

2. For macs, make sure you have [HomeBrew](https://brew.sh/)

## Setup

1. First I suggest cloning this repo to better follow along with the material:

```shell
git clone https://github.com/twclark0/sql-advanced.git

cd sql-advanced

```

2. Install postgres on your machine. When it's install, make sure you can connect to your local DB by running `psql postgres` in your terminal

#### Macs

- Use HomeBrew `brew install postgresql`

#### Windows

- Follow the instructions here: https://www.postgresql.org/download/windows/

3. Once you have postgres running locally, run the following commands:

```sql
create table users (
user_handle uuid Primary key,
first_name text,
last_name text,
email text
);

```

```sql
create table purchases (
date date,
user_handle uuid,
sku uuid,
quantity int
);

```

```sql
create table members (
start_date date,
end_date date,
user_handle uuid,
first_name text,
email text
);

```

## Working through it

Each lesson is separated by different folders. Within each folder is a `notes.md` file that holds the information we will be going through. Each notes markdown file is broken down into three main sections. This includes a general "notes", "syntax", and a "exercise" block. I will work top to bottom, starting off by giving some general notes about each lesson. This includes which versions of postgres support this concept, some common gotchas, and other tips. We'll then review its syntax and then walk through an exercise of this concept.

## License

This material is available for private, non-commercial use under the
[GPL version 3](http://www.gnu.org/licenses/gpl-3.0-standalone.html). If you have questions about using this workshop material in anyway, please contact me
at `tyler@tylerclark.dev`
