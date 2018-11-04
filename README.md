# Vue.js Mail Example Application

This repository contains the code for the second example application from the [Vue.js: From Beginner to Professional course](https://l.codingexplained.com/course/vuejs?src=github).

## Getting up and Running

``` bash
# Install the dependencies
npm install

# Serve with hot reload at http://localhost:8080
npm run dev

# Build for production with minification
npm run build
```
In the sidebar component enter in the following html markup: 

<aside class="sm-side">
    <div class="user-head">
        <img src="src/assets/images/profile.jpg">

        <div class="user-name">
            <h5>Bo Andersen</h5>
            <span class="email-address">info@codingexplained.com</span>
        </div>
    </div>

    <ul class="inbox-nav">
        <li class="active">
            <a href="#">
                <i class="fa fa-inbox"></i>Inbox <span class="label label-danger pull-right">?</span>
            </a>
        </li>

        <li>
            <a href="#">
                <i class="fa fa-envelope-o"></i>Sent <span class="label label-default pull-right">?</span>
            </a>
        </li>

        <li>
            <a href="#">
                <i class="fa fa-bookmark-o"></i>Important <span class="label label-warning pull-right">?</span>
            </a>
        </li>

        <li>
            <a href="#">
                <i class=" fa fa-trash-o"></i>Trash <span class="label label-default pull-right">?</span>
            </a>
        </li>
    </ul>
</aside>

Register the sidebar component locally within the App.vue file and use the component tag name within the template:

<template>
    <div class="container">
        <div class="mail-box">
            <app-sidebar></app-sidebar>
        </div>
    </div>
</template>

<script>
    import Sidebar from './Sidebar.vue';

    export default {
        components: {
            appSidebar: Sidebar
        }
    }
</script>

