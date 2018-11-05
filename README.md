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

Register the sidebar component locally within the App.vue file and use the component tag name within the template.
Do so with the Content component too:

<template>
    <div class="container">
        <div class="mail-box">
            <app-sidebar></app-sidebar>
            <app-content><app-content>
        </div>
    </div>
</template>

<script>
    import Sidebar from './Sidebar.vue';

    export default {
        components: {
            appSidebar: Sidebar,
            appContent: Content
        }
    }
</script>

We need the Content component to render the correct component, depending on what component we are currently on.
We can use the <component> tag for this.
Inside of the Content component, use the <component> tag in the template.
Make it dynamic with the ":is" attribute with the v-bind directive.
Remember to use the <keep-alive> tag to improve performance.

We will store the component to be rendered in the template, in a data property.
We will need a way to navigate back from one view to another, so add a histroy data property containing an array of objects.
Each object will contain a tag name of the component and the views title.
For now we will create the default component to be rendered when the page is first loaded.


To refer to the view that should currently be displayed, use a computed property.
Inside of that add a property for 'currentView'.
This will return the first index of the history array.
Now we can use this currentView computed property within the template.
Access the correct tag by using 'currentView.tag' to access the correct tag name in the history array.
Then render the title of the currentView with currentView.title in a h3 tag.

Finally, import the inbox component and create a components key.
Before we can add the logic to switch between other views, add in the other components locally.
Import Sent, Important, Trash and ViewMessage:

<template>
    <aside class="lg-side">
        <div class="inbox-head">
            <h3>{{ currentView.title }}</h3>
        </div>

        <keep-alive>
            <component :is="currentView.tag"></component>
        </keep-alive>
    </aside>
</template>

<script>
    import Inbox from './Inbox.vue';

    export default {
        data() {
            return {
                history: [
                    {
                        tag: 'app-inbox',
                        title: 'Inbox'
                    }
                ]
            };
        },
        computed: {
            currentView() {
                return this.history[0];
            }
        },
        components: {
            appInbox: Inbox
        }
    }
</script>

We need to be able to change the component for the output.
When we click a menu item there needs to be communication between the sidebar component and the content component.
We will use an event bus to trigger an event when a menu item is clicked.

In main.js create the event bus:

export const eventBus = new Vue();

In the sidebar component add some click event handlers.
Use the @click event hander with the prevent modifier and pass on the navigate method and pass the tag name and title into that.

Import the event bus.
Pass in 'newView' the as the tag name for the navigate methods first arg and pass in 'title' for it's second arg.
$emit the event bus and call the event 'changeView' then pass an object of data.
We pass the tag with the newView parameter, and the title as title:

<template>
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
                <a href="#" @click.prevent="navigate('app-inbox', 'Inbox')">
                    <i class="fa fa-inbox"></i>Inbox <span class="label label-danger pull-right">?</span>
                </a>
            </li>

            <li>
                <a href="#" @click.prevent="navigate('app-sent', 'Sent')">
                    <i class="fa fa-envelope-o"></i>Sent <span class="label label-default pull-right">?</span>
                </a>
            </li>

            <li>
                <a href="#" @click.prevent="navigate('app-important', 'Important')">
                    <i class="fa fa-bookmark-o"></i>Important <span class="label label-warning pull-right">?</span>
                </a>
            </li>

            <li>
                <a href="#" @click.prevent="navigate('app-trash', 'Trash')">
                    <i class=" fa fa-trash-o"></i>Trash <span class="label label-default pull-right">?</span>
                </a>
            </li>
        </ul>
    </aside>
</template>

<script>
    import { eventBus } from './main';

    export default {
        methods: {
            navigate(newView, title) {
                eventBus.$emit('changeView', {
                    tag: newView,
                    title: title
                });
            }
        }
    }
</script>

Then in the Content component import the event bus.
Then add a listener for this event, within the 'created' lifecycle hook.
Use $on to listen for the 'changeView' event, with a call back with some data.
Store the newView that we are going to display into a temporary array.
It will contain an object with this newView.
Access the newView tag and title with data.tag and data.title.
Set the history property to this temp array and insert this newView as the first item of the history array and concatenate them.
Now the sidebar menu items should chage views when clicked: 

...
import { eventBus } from './main.js';

export default {
    created() {
        eventBus.$on('changeView', (data) => {
            let temp = [{
                tag: data.tag,
                title: data.title
            }];

            this.history = temp.concat(this.history.splice(0));
        });
    },
...

Lets highlight the current view in the menu.
Keep track of the currentView with a data property in the sidebar component.
Create an 'activeView' property which we will set to 'app-inbox' initially.
Now we will listen for the events that have been triggered within this component.
Make a created lifecycle hook and use the eventBus.$on to listen for the 'changeView' event.
Then have a call back with some data being passed along.
Set the activeView to data.tag:

export default {
  created() {
      eventBus.$on('changeView', (data) => {
          this.activeView = data.tag;
      });
  },

Now we need use this data property to set the active class to the correct li element in the template.
Use the v-bind directive with the class attribute.
Pass in an object and set active to the activeView data property and set that equal to the tag name.
So if this is true, the active class will be added:

<ul class="inbox-nav">
    <li :class="{ active: activeView === 'app-inbox'}">
        <a href="#" @click.prevent="navigate('app-inbox', 'Inbox')">
            <i class="fa fa-inbox"></i>Inbox <span class="label label-danger pull-right">?</span>
        </a>
    </li>

    <li :class="{ active: activeView === 'app-sent'}">
        <a href="#" @click.prevent="navigate('app-sent', 'Sent')">
            <i class="fa fa-envelope-o"></i>Sent <span class="label label-default pull-right">?</span>
        </a>
    </li>

    <li :class="{ active: activeView === 'app-important'}">
        <a href="#" @click.prevent="navigate('app-important', 'Important')">
            <i class="fa fa-bookmark-o"></i>Important <span class="label label-warning pull-right">?</span>
        </a>
    </li >

    <li :class="{ active: activeView === 'app-trash'}">
        <a href="#" @click.prevent="navigate('app-trash', 'Trash')">
            <i class=" fa fa-trash-o"></i>Trash <span class="label label-default pull-right">?</span>
        </a>
    </li>
</ul>

Loading messages from a file called messages.js to display in the e-mails.
They will be objects that contain other parts of information in each message.
Looking something like this:

{
    subject: '',
    content: `
        <html>
    `,
    isImportant: ,
    isDeleted: ,
    isRead: ,
    type: '',
    date: ,
    from: {
        name: '',
        email: ''
    },
    atatchments: []
}

Import this file to the App.vue file:

import messages from './data/messages';

Add a data property for the messages within this component so we can keep track of the messages globally:

export default {
        data() {
            return {
                messages: messages
            };
        }
    }

Let's look at adding numbers to the sidebar menu to show how many messages there are in each category.
Add a prop to the sidebar component with a messages property:

export default {
    props: {
        messages: {
            type: Array,
            required: true
        }
    },

Now we can pass in the messages from the App component.
Use the v-bind directive and refer to the messages data property in the template in App.vue:

<div class="mail-box">
    <app-sidebar :messages="messages"></app-sidebar>
    <app-content></app-content>
</div>

In the sidebar component, let's group the messages by their status.
We want a property to access the unread messages.
Add a computed property for each message state which then filters the collection of all of the messages.
Use a boolean value to determine whether the message is unread by checking if it is incoming, and has not been read, and has not been deleted.
Do the same for sent messages, important messages, and deleted:

computed: {
    unreadMessages() {
        return this.message.filter(function(message) {
            return (message.type == 'incoming' && !message.isRead && !message.isDeleted);
        });
    },
    sentMessages() {
    return this.message.filter(function(message) {
        return (message.type == 'outgoing' && !message.isDeleted);
    });
    },
    importantMessages() {
        return this.message.filter(function(message) {
            return (message.type == 'incoming' && message.isImportant && !message.isDeleted);
        });
    },
    trashedMessages() {
        return this.message.filter(function(message) {
            return message.isDeleted == true;
        });
    },
}

These will return arrays so we can find the length of each array to display as the number for each message state.
For each message state, display it in the template next to the correct menu item in the sidebar with string interpolation.
Eg:

{{ unreadMessages.length }}

Now it should display the correct amounts in the sidebar.






