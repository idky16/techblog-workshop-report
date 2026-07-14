---
title: "Week 2"
date: 2026-04-27
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

## Time

27/04/2026 - 03/05/2026

## Weekly Objectives

- Understand the existing TechBlog source before proposing any AWS changes.
- Verify that the local application and database form a stable deployment baseline.

## Work Completed

- Reviewed the Java 17, Spring Boot 3.5.12, Spring MVC, Spring Security, Spring Data JPA/Hibernate, Thymeleaf, MySQL, and Maven Wrapper stack.
- Confirmed that frontend and backend are packaged together as one MVC monolith rather than a separate React application.
- Ran TechBlog at `http://localhost:8080` and checked the local `techblog` database in Laragon/MySQL.
- Located legacy upload folders `storage/avatars` and `storage/posts` and traced how templates display those files.

## Results and Lessons

- Produced an inventory of application, database, upload, and runtime dependencies.
- Confirmed that Amazon EC2 is a direct fit for running the monolithic executable JAR.
- Established the compatibility requirement: existing local images must continue to render after S3 integration.
