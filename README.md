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

Let's look at displaying messages in the inbox.
We need to pass the messages from the App component to the Content component.
In the Content component add a property object with a messages key that will also be an object.
Set its type to an array and make it required:

props: {
    messages: {
        type: Array,
        required: true
    }
}

Then in the App component use the v-bind directive on the <app-content> tag on the messages property:

<app-content :messages="messages"></app-content>

Now the messages are available in the Content component.
Now we need to pass them to the dynamic component - the activeView.

In the history data object, add in a messages property and set it to null so we can make it reactive:

history: [
    {
        tag: 'app-inbox',
        title: 'Inbox',
        data: {
            messages: null
        }
    }
]

In the template we can pass the current views data object with the v-bind directive.
We will pass the object to a data prop that we will add in soon.
Set it to the currentView data object:

<component :is="currentView.tag" :data="currentView.data"></component>

We need to modify the currentView property.
Set the messages property on the data object and always pass the messages along to the view.
In the currentView computed property, take the current view and store it in a variable called 'current'.
On this variable set the messages on the data object to this.messages.
Return the current variable.
This ensures the messages are always part of the data object:

computed: {
    currentView() {
        let current = this.history[0];
        current.data.messages = this.messages;
        return current;
    }
},

Within our listener for the changeView event we need to handle the case where no data object is passed along in the event.
In the created lifecycle hook, add a data property.
Set it to the data that is passed along in the event.
If it does not contain a truthy value, set it to an empty object:

created() {
    eventBus.$on('changeView', (data) => {
        let temp = [{
            tag: data.tag,
            title: data.title,
            data: data.data || {}
        }];
        this.history = temp.concat(this.history.splice(0));
    });
},

Now we have what we need for the inbox view.
In the inbox component add in a props for accepting the data object.
Add a props key with a data object that is required:

props: {
    data: {
        type: Object,
        required: true
    }
}

We need to filter this incoming data so we only display the incomingMessages.
Create a computed property and add an expression to check whether the messages are incoming:

computed: {
    incomingMessages() {
        return this.data.messages.filter(function(message) {
            return (message.type == 'incoming' && !message.isDeleted);
        });
    }
}

Now let's make some html markup to display these messages.
We will use this in multiple components so make it into it's own component.
So in the src folder create a Messages.vue file.
Then use the props key to take in the messages data:

export default {
    props: {
        messages: {
            type: Array,
            required: true
        }
    }
}

Back in the Inbox component, register the Messages component and add it to the template:

import Messages from './Messages.vue';

Then add a local components key and give it a tag of appMessages and pass in the Messages object:

components: {
    appMessages: Messages
}

Then in the template create a div with the class of inbox body.
Inside of that put the <app-messages> tag.
Bind the messages to it using the computed property incomingMessages:

<div class="inbox-body">
    <app-messages :messages="incomingMessages"></app-messages>
</div>

In the Messages component put in the html markup in the template:

<template>
    <table class="table table-inbox table-hover">
        <tbody>
            <tr>
                <td><input type="checkbox"></td>
                <td>
                    <a href="#" >
                        <i class="fa fa-star"></i>
                    </a>
                </td>
                <td>from</td>
                <td>subject</td>
                <td><i class="fa fa-paperclip"></i></td>
                <td class="text-right">date</td>
            </tr>
        </tbody>
    </table>

    <p>No messages here yet.</p>
</template>

Now we need to make this template dynamic.
Use the v-if directive to display the table only if there are messages.
Then loop through the messages array and add a table row for each message, and for the unread messages add a highlighted class using the v-bind directive.
Then only display the star icon if the message is important.
Output who the message is from and the subject.
Make it so it will only display the paperclip icon if there is an attatchment.
Display the data.
Then if there are no messages display a paragraph element:

<template>
    <table v-if="messages.length > 0" class="table table-inbox table-hover">
        <tbody>
            <tr v-for="message in messages" :class="{ unread: typeof message.isRead !== 'undefined' && !message.isRead }">
                <td><input type="checkbox"></td>
                <td>
                    <a href="#" v-if="typeof message.isImportant !== 'undefined'">
                        <i class="fa fa-star"></i>
                    </a>
                </td>
                <td>{{ message.from.name }}</td>
                <td>{{ message.subject }}</td>
                <td><i v-if="message.attachments.length > 0" class="fa fa-paperclip"></i></td>
                <td class="text-right">{{ message.date.fromNow() }}</td>
            </tr>
        </tbody>
    </table>

    <p v-else>No messages here yet.</p>
</template>

Let's add a feature to mark a message as important.
In the messages component add a click event handler on the important icon in the template.
Set it to message, which is the alias used within the v-for loop.
Set message.isImportant = !message.isImportant which will switch the property's value around every time we click it.
Then we want to add a style to this class so the important messages stand out.
We will use the v-bind directive to make it dynamic as we are only going to add the isImportant class if the isImportant property is true.
Put the classes in an array, then we will combine the two classes by putting an object syntax in the array.
Set the isImportant class dynamically using a boolean expression to check if isImportant property is true:

<a href="#" v-if="typeof message.isImportant !== 'undefined'" @click.prevent.stop="message,isImportant = !message.isImportant">
    <i :class="['fa', 'fa-star', { important: message.isImportant }]"></i>
</a>
