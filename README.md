# Advanced SQL

✌️Hello! My name is Tyler Clark and this is the
source material for my Advanced SQL workshop

## Why Should You Care About this Workshop?

Let's face it, data powers the web. And I am not only talking about the twitter data that is queried out to show you your feed. I'm talking about analytics, artificial intelligence, and

---

## Pre-Workshop Instructions/Requirements

This workshop is helpful for developers that have some basic knowledge of SQL.

- [ ] Setup the project (follow the setup instructions below)
- [ ] Install and setup [Zoom](https://zoom.us) on the computer you will be using

## Workshop Outline

While there are similarities between each lesson, we will work through each exercise independent of each other.

1.

## System Requirements

- [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) v2 or greater

Git must be available in your `PATH`. To verify things are set up
properly, you can run this:

```shell
git --version
```

If you have trouble with any of these, learn more about the PATH environment
variable and how to fix it here for [windows][win-path] or
[mac/linux][mac-path]

## Setup

First I suggest cloning this repo to better follow along with the material:

```shell
git clone https://github.com/twclark0/sql-advanced.git

cd sql-advanced

```

Once you have postgres running locally, run the following commands:

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

I will have at least one exercise for each lesson. All of the exercises will be in the `exercises` directory. At the top of each exercise is a description of what the current state of the code is in and what the goal state is supposed to look like. So before you have any questions please make sure you have read the description at the top of the file thoroughly.

As you will see, there is a directory called `exercises-completed` that shows the final "answer" to each exercise question.

## License

This material is available for private, non-commercial use under the
[GPL version 3](http://www.gnu.org/licenses/gpl-3.0-standalone.html). If you have questions about using this workshop material in anyway, please contact me
at `tyler@tylerclark.dev`
