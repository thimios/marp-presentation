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
<!-- _header: "" -->
<!-- _footer: "" -->

![](./big-logo.png)
# Hands-On with ArchivesSpace
## Docker Installation, Customization, and API Integration
## March 2026

---

<!-- footer: "![w:300](./big-logo.png)" -->
<!-- header: "![w:80](./small-logo.png) Get Docker Desktop" -->

## What is Docker
- Running applications using containers:
  - Lightweight, isolated environments
  - Can run on any infrastructure

## How to Install Docker Desktop
- https://docs.docker.com/get-started/get-docker/

---

<!-- header: "![w:80](./small-logo.png) ArchivesSpace on Docker" -->

## Get the ArchivesSpace Docker Configuration Package
- https://github.com/archivesspace/archivesspace/releases/tag/v4.1.1

## Documentation
- https://docs.archivesspace.org/administration/docker/

---

<!-- header: "![w:80](./small-logo.png) Docker Conf Package" -->

```
.
├── backups
├── config
│   └── config.rb
├── locales
├── plugins
├── proxy-config
│   └── default.conf
├── sql
├── docker-compose.yml
└── .env
```

<!--
- backups directory: first created once you start the application. Will contain the automatically performed backups of the database. See Automated Backups section.
- config/config.rb: contains the main configuration of ArchivesSpace.
- locales: allows customization of the UI text.
- plugins: accommodates additional ArchivesSpace plugins. By default contains the local and lcnaf plugins.
- proxy-config/default.conf: contains the configuration of the bundled nginx, see also proxy configuration.
- sql: put your .sql database dump file here to initialize the new database, see next section.
- docker-compose.yml: contains all the information required by Docker to build and run ArchivesSpace.
- .env: contains configuration of the docker containers including:
  - Credentials used by ArchivesSpace to access its MySQL database. Recommended to change default root and user passwords.
  - The database connection URI which should also be updated accordingly after the database user password is updated.
  -->

---
<!-- header: "![w:80](./small-logo.png) Initialize with Demo Data​" -->

## Use our demo database

- Download into the ./sql directory
  - https://github.com/archivesspace/archivesspace/blob/master/build/mysql_db_fixtures/demo.sql.gz​

- No need to decompress the file

---
<!-- header: "![w:80](./small-logo.png) Fire up ArchivesSpace!​" -->

- Change to the extracted directory and run:

    ```
    docker compose up --detach​
    ```

- Watch logs in Docker Desktop

- Remove the `demo.sql.gz` file from the `sql` directory

---
<!-- header: "![w:80](./small-logo.png) Configuring ArchivesSpace​​" -->

- Change text with locales
- Change the logo

---
<!-- header: "![w:80](./small-logo.png) Change UI text with locales" -->

- Locales are exposed in the `locales` directory
- Edit `locales/public/en.yml`

    ```
    en:
      brand:
        title: ArchivesSpace Public Interface
        title_link_text: Return to the ArchivesSpace homepage
        welcome_head: Welcome to ArchivesSpace
        welcome_message: |
          <p>Search across our collections, digital materials, and more.</p>
        welcome_page_title: ArchivesSpace Public Interface
    ```
    
- Restart `archivesspace` container on Docker Desktop for changes to take effect

---
<!-- header: "![w:80](./small-logo.png) Change PUI and SUI logos" -->

- Update config variables in `config/config.rb`

    ```
    AppConfig[:pui_branding_img] = '/assets/images/custom-logo.svg'
    AppConfig[:pui_branding_img_alt_text] = '2026 Code4lib Woskshop - public interface'

    AppConfig[:frontend_branding_img] = '/assets/images/custom-logo-staff.svg'
    AppConfig[:frontend_branding_img_alt_text] = 'ArchivesSpace - staff interface'
    ```
- See also https://docs.archivesspace.org/customization/theming/


---
<!-- header: "![w:80](./small-logo.png) Let's install some Plugins" -->

- Digitization Work Order Plugin
  - By Hudson Molonglo
  - Provides the ability to download reports for sets of components under a resource for the purpose of creating digitization work orders.

- Content Warnings Plugin
  - By Digital Library Technologies Group, Dartmouth College Library
  - Allows staff users to add content warnings and optional clarifying descriptions of why the warning has been applied.
---
<!-- header: "![w:80](./small-logo.png) Plugin Installation overview​" -->

- Place the plugin code in the plugins directory​
- Add plugin name to the conf file​
  - Order matters​
- Optionally (depending on the plugin requirements)
  - execute plugin initialization
  - run plugin database migrations
- Restart ArchivesSpace server​

---

<!-- header: "![w:80](./small-logo.png) Digitization Work Order Plugin​" -->
- Download and follow instructions:
  - https://github.com/hudmol/digitization_work_order​

- Run commands on the container:
  - Docker Desktop -> archivesspace container -> Exec tab

    ```
    $ cd archivesspace
    $ ./scripts/initialize-plugin.sh digitization_work_order
    ```
---

<!-- header: "![w:80](./small-logo.png) Content Warnings Plugin​" -->

- Download and follow instructions:
  - https://github.com/dartmouth-dltg/aspace_content_warnings​

- Run commands on the container:
  - Docker Desktop -> archivesspace container -> Exec tab
      ```
      $ cd archivesspace/​
      $ ./scripts/setup-database.sh​
      ```

- Add translations for new Controlled List value​
  - Locale files are in the `locales` directrory on the host system
---
<!-- header: "![w:80](./small-logo.png) Database administration​​" -->

- Backup in mysql container -> exec​ tab

    ```
    mysqldump -u root -p123456 archivesspace | gzip > /tmp/db.$(date +%F.%H%M%S).sql.gz​
    ```

- Download the file in the Files tab of the mysql container​
- Manipulate a resource record:
  - http://localhost/staff/resources/6/edit​

- Restore: mysql container -> exec​ tab

    ```
    gunzip -c /tmp/db.2026-02-17.155254.sql.gz | mysql -uas -pas123 archivesspace​
    ```

<!--
- Backup:
  - https://docs.archivesspace.org/administration/backup/#using-the-docker-configuration-package​

- Restore:
  - https://docs.archivesspace.org/administration/backup/#when-running-on-docker​
  -->

---

<!-- header: "![w:80](./small-logo.png) Solr administration​" -->
- Reindex​

  ```
  docker compose down solr app​
  docker volume rm archivesspace_app-data archivesspace_solr-data​
  docker compose up –d​
  ```
- Solr Admin interface​
  - http://localhost:8983/solr/#/​

---
<!-- header: "![w:80](./small-logo.png) ArchivesSpace API​​" -->

- Get Postman
  - https://www.postman.com/downloads/
- Documentation 
  - Introduction 
      - https://docs.archivesspace.org/api/
  - API reference
    - https://archivesspace.github.io/archivesspace/api/#introduction
  - API Playbook - Introduction to API workshop
    - https://support.atlas-sys.com/hc/en-us/articles/360052217114-The-ArchivesSpace-API-Playbook​
    - https://www.youtube.com/watch?v=ajaYbtKR1BE&t=234s
---

<!-- header: "![w:80](./small-logo.png) Using Postman" -->

- Create an "ArchivesSpace collection
  - Collection Variables:
    - baseUrl: http://localhost/staff/api/
    - sessionToken: empty for now, we will get it from the login response
  - Authorization:
    - Auth type: API Key
      - Key: `X-ArchivesSpace-Session`
      - Value: `{{sessionToken}}`
      - Add to: Header

---

<!-- header: "![w:80](./small-logo.png) Login" -->

- Create a Loginq Request
  - Method: `POST`
  - URL: `{{baseUrl}}/users/admin/login?password=admin`
  - Click Send
  - Get the sessionToken from the response and update the `sessionToken` variable in the "ArchivesSpace" collection

---
<!-- header: "![w:80](./small-logo.png) List Repositories" -->

- Create a new request
  - Method: `GET`
  - URL: `{{baseUrl}}/repositories`
  - Click Send
  - Get the first repository id from the response

---
<!-- header: "![w:80](./small-logo.png) List Resources" -->

- Create a new request
  - Method: `GET`
  - URL: `{{baseUrl}}/repositories/2/resources?page=1`
  - Click Send

---
<!-- header: "![w:80](./small-logo.png) List Accessions" -->

- Create a new request
  - Method: `GET`
  - URL: `{{baseUrl}}/repositories/2/accessions?page=1`
  - Click Send

---
<!-- header: "![w:80](./small-logo.png) List Agent Families" -->

- Create a new request
  - Method: `GET`
  - URL: `{{baseUrl}}/agents/families?page=1`
  - Click Send

---
<!-- header: "![w:80](./small-logo.png) Create an Agent Family" -->

  - Create a new request
  - Method: `POST`
  - URL: `{{baseUrl}}/agents/families`
  - Body (raw)
    ```
    {
      "jsonmodel_type": "agent_family",
      "dates_of_existence": [...
    ```

