---
weight: 200
title: "API"
description: "API Help"
icon: "edit"
date: "2025-01-15"
lastmod: "2025-01-15"
draft: false
---

API tokens can be created to perform actions, access file information, and update user settings just like what can be done from the UI. You can create API tokens from the settings page via "API Management" section. This section will only show up if the user has "API" permissions, which can be granted by editing the user in user management.

Regardless of whether a user has API permissions, anyone can visit the swagger page which is found at `/swagger`. This swagger page uses a short-live token (2-hour exp) that the UI uses, but allows for quick access to all the API's and their described usage and requirements:

![image](https://github.com/user-attachments/assets/12abd1f6-21d3-4437-98ed-9b0da6cf2c73)

When using the API outside of swagger, you will need to set the API token as a bearer token authentication type. This means the authorization header will look like `Authorization: Bearer <token>`. For example in Postman:

Successful Request:

<p align="center"><img width="500" alt="image" src="https://github.com/user-attachments/assets/4f18fa8a-8d87-4f40-9dc7-3d4407769b59"></p>

Failed Request

<p align="center"><img width="500" alt="image" src="https://github.com/user-attachments/assets/4da0deae-f93d-4d94-83b1-68806afb343a"></p>
