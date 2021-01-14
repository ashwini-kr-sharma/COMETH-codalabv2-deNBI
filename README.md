## A steb-by-step guide to hosting Codalab-v2 instance and compute workers in de.NBI cloud

## 0. Prerequisites

This tutorial presumes that you have already performed these steps -

- Created a valid functioning deNBI cloud account
- Requested and obtained the necessary computational resources for setting up a virtual machine (VM)

---

If not, fulfil these prerequisites by following these steps - 

- Create an Elixir account
An Elixir account can be most simply created if you have a valid account for any of these participating [German institutions](https://elixir-europe.org/about-us/who-we-are/nodes/germany). However, Elixir accounts can also be created through Google account. 

> **NOTE:** During the Elixr account registration process kindly note down your _Elixir username_. This will be needed and will come handy later on.

- Create a deNBI account
Do your account registration and request for a project resources allocation (#CPU, memory, storage etc) by following the instructions provided here - 

1. [Getting started](https://cloud.denbi.de/get-started/)
2. [Registration](https://cloud.denbi.de/wiki/registration/)
3. [Resource allocation](https://cloud.denbi.de/wiki/portal/allocation/)

Ok, so now you have a deNBI cloud account and the Admins have sanctioned your project resources. Let's continue with the next steps.

## 1. Getting familiar with the deNBI dashboard

Goto https://cloud.denbi.de/ and login (click PORTAL LOGIN)

Under Project Management (on the left), click on your (1) project name and (2) click login URL

