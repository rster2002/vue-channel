# Vue Channel

Vue channel is a small package that allows you to sync data between componets.

### Installation

To install Vue Channel into your project, use npm:

```
npm install vue-channel
```

or use yarn

```
yarn add vue-channel
```

### Example Usage

Vue Channel allow you to make a channel (who would have guessed) and make that channel broadcast data to other components.

Say we have multiple componets that display the users information after they've loged in. Let's say a component to display the users profile picture and a component for their general information (username and email). The javascript for such components would look something like this:

Profile picture

```javascript
export default {
    data() {
        return {
            url: "<some placeholder url>"
        }
    }
}
```

General info

```javascript
export default {
    data() {
        return {
            username: "<placeholder username>",
            email: "<placeholder email>"
        }
    }
}
```

When the user logges in, we need to update the values to the users information. Using Vue Channel we can listen to changes made on the channel, that we would call 'userInfo'. So our componets would look something like this:

Profile picture

```javascript
import vueChannel from "vue-channel";

export default {
    data() {
        return {
            url: "<some placeholder url>"
        }
    },
    created() {
        vueChannel("userInfo")
            .receive(user => {
				this.url = user.profilePicture;
            });
    }
}
```

General info

```javascript
import vueChannel from "vue-channel";

export default {
    data() {
        return {
            username: "<placeholder username>",
            email: "<placeholder email>"
        }
    },
    created() {
        vueChannel("userInfo")
            .receive(user => {
				this.username = user.name;
            	this.email = user.email;
            });
    }
}
```

When the component is created, we start listening to changes made to the channel. When we start listening, it check if there is anything on the channel, and if there is, it fires once after it registered to the channel.

Now in our sign in component, where we get the user's data, we have something like this:

```javascript
import vueChannel from "vue-channel";

export default {
    methods: {
        afterLogin(user) {
            vueChannel("userInfo")
            	.set(user);
        }
    }
}
```

And that's it. Vue Channel has some other functionalities that you can find below.

### Overview

Here are the function's that are available though Vue Channel. First, the method is shown, and then an example is given after which there will be a description of what it does.

#### set(state)

```javascript
vueChannel("userInfo")
    .set({
		name: "Arthur Dent",
    	email: "babelfish@vogon.uni",
    	profilePicture: "(see guide)"
    });
```

Using the `set` function, we overwrite any other state that is on that channel.

#### update(state)

```javascript
vueChannel("userInfo")
    .update({
    	email: "ihatevogonpoetry@magrathea.uni"
    });
```

In contrast to `set`, `update` only overwrites new values.

#### receive(callback)

```javascript
vueChannel("userInfo")
	.receive(state => {
    	// Do stuff
	});
```

When the state is updated, this method will be called where you can use the state to update the state of your component. If the state is not empty when the receiver is created, it will fire once.

#### disposable(callback)

```javascript
vueChannel("userInfo")
	.disposable(state => {
    	// Do stuff
	});
```

A disposable is usefull when handeling with an asyncronous state, like when getting userdata from a database. The disposable will fire when the state changed and will delete itself, so it will only be fired once. You can keep the disposable alive by returning `true` like this:

```javascript
vueChannel("userInfo")
	.disposable(state => {
    	// Do stuff
    	return true;
	});
```

#### state

```javascript
vueChannel("userInfo").state;
```

Simple returns the current state of the channel.