---
theme: default
paginate: false
---
<style>
section.title {
  text-align: center;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
header {
  font-size: 64px;
  display: flex;
  align-items: center;
  text-align: center;
  gap: 10px;
}
footer {
  position: absolute;
  bottom: 20px;
  right: 20px;
  left: unset;
  padding: 0;
}
</style>

<!-- _class: title -->
![](./big-logo.png)

# Hands-On with ArchivesSpace​
## Docker Installation, Customization, and API Integration​
## March 2026​

---
<!-- footer: "![w:300](./big-logo.png)" -->
<!-- header: "![w:80](./small-logo.png) Get Docker Desktop​​" -->
## What is Docker​

- running applications using containers:
  - lightweight, isolated environments
  - can run on any infrastructure

## How to install Docker Desktop​

- https://docs.docker.com/get-started/get-docker/​

---
<!-- header: "![w:80](./small-logo.png) ArchivesSpace on Docker​" -->

## Get the ArchivesSpace Docker Configuration Package​

- https://github.com/archivesspace/archivesspace/releases/tag/v4.1.1

## Documentation

- https://docs.archivesspace.org/administration/docker/​

---
<!-- header: "![w:80](./small-logo.png) Docker Conf Package​​" -->

```
    .​
    ├── backups​
    ├── config​
    │ └── config.rb​
    ├── locales​
    ├── plugins​
    ├── proxy-config​
    │ └── default.conf​
    ├── sql​
    ├── docker-compose.yml​
    └── .env​
```
​

