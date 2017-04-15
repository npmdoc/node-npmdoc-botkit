# api documentation for  [botkit (v0.5.2)](https://botkit.ai)  [![npm package](https://img.shields.io/npm/v/npmdoc-botkit.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-botkit) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-botkit.svg)](https://travis-ci.org/npmdoc/node-npmdoc-botkit)
#### Building blocks for Building Bots

[![NPM](https://nodei.co/npm/botkit.png?downloads=true&downloadRank=true&stars=true)](https://www.npmjs.com/package/botkit)

[![apidoc](https://npmdoc.github.io/node-npmdoc-botkit/build/screenCapture.buildCi.browser.apidoc.html.png)](https://npmdoc.github.io/node-npmdoc-botkit/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-botkit/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-botkit/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "ben@howdy.ai"
    },
    "bugs": {
        "url": "https://github.com/howdyai/botkit/issues"
    },
    "dependencies": {
        "async": "^2.0.0-rc.5",
        "back": "^1.0.1",
        "body-parser": "^1.14.2",
        "botbuilder": "^3.2.3",
        "botkit-studio-sdk": "^1.0.0",
        "ciscospark": "^0.7.31",
        "clone": "2.0.0",
        "command-line-args": "^3.0.0",
        "crypto": "0.0.3",
        "express": "^4.13.3",
        "https-proxy-agent": "^1.0.0",
        "jfs": "^0.2.6",
        "localtunnel": "^1.8.1",
        "md5": "^2.1.0",
        "mustache": "^2.2.1",
        "promise": "^7.1.1",
        "randomstring": "^1.1.5",
        "request": "^2.67.0",
        "twilio": "^2.9.1",
        "url": "^0.11.0",
        "ware": "^1.3.0",
        "ws": "^1.1.1"
    },
    "description": "Building blocks for Building Bots",
    "devDependencies": {
        "jscs": "^2.7.0",
        "mocha": "^2.4.5",
        "should": "^8.0.2",
        "should-sinon": "0.0.5",
        "sinon": "^1.17.7",
        "winston": "^2.1.1"
    },
    "directories": {},
    "dist": {
        "shasum": "4be4261cbdd81ba70d7dab225864c87d6bb4ad81",
        "tarball": "https://registry.npmjs.org/botkit/-/botkit-0.5.2.tgz"
    },
    "gitHead": "5e14ba95c6de4d9ef245e9bd04265cfa3c2c1e59",
    "homepage": "https://botkit.ai",
    "keywords": [
        "bots",
        "chatbots",
        "slack",
        "cisco spark",
        "facebook messenger",
        "twilio ipm",
        "microsoft bot framework"
    ],
    "license": "MIT",
    "main": "lib/Botkit.js",
    "maintainers": [
        {
            "name": "xoxco"
        }
    ],
    "name": "botkit",
    "optionalDependencies": {},
    "repository": {
        "type": "git",
        "url": "git+https://github.com/howdyai/botkit.git"
    },
    "scripts": {
        "pretest": "jscs ./lib/",
        "test": "mocha tests/*.js"
    },
    "version": "0.5.2"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module botkit](#apidoc.module.botkit)
1.  [function <span class="apidocSignatureSpan">botkit.</span>botframeworkbot (configuration)](#apidoc.element.botkit.botframeworkbot)
1.  [function <span class="apidocSignatureSpan">botkit.</span>consolebot (configuration)](#apidoc.element.botkit.consolebot)
1.  [function <span class="apidocSignatureSpan">botkit.</span>core (configuration)](#apidoc.element.botkit.core)
1.  [function <span class="apidocSignatureSpan">botkit.</span>facebookbot (configuration)](#apidoc.element.botkit.facebookbot)
1.  [function <span class="apidocSignatureSpan">botkit.</span>slackbot (configuration)](#apidoc.element.botkit.slackbot)
1.  [function <span class="apidocSignatureSpan">botkit.</span>sparkbot (configuration)](#apidoc.element.botkit.sparkbot)
1.  [function <span class="apidocSignatureSpan">botkit.</span>twilioipmbot (configuration)](#apidoc.element.botkit.twilioipmbot)



# <a name="apidoc.module.botkit"></a>[module botkit](#apidoc.module.botkit)

#### <a name="apidoc.element.botkit.botframeworkbot"></a>[function <span class="apidocSignatureSpan">botkit.</span>botframeworkbot (configuration)](#apidoc.element.botkit.botframeworkbot)
- description and source-code
```javascript
function BotFrameworkBot(configuration) {

    // Create a core botkit bot
    var bf_botkit = Botkit(configuration || {});

    // customize the bot definition, which will be used when new connections
    // spawn!
    bf_botkit.defineBot(function(botkit, config) {

        var bot = {
            botkit: botkit,
            config: config || {},
            utterances: botkit.utterances,
        };

        bot.startConversation = function(message, cb) {
            botkit.startConversation(this, message, cb);
        };

        bot.send = function(message, cb) {
            function done(err) {
                if (cb) {
                    cb(err);
                }
            }

            if (!message || !message.address) {
                if (cb) {
                    cb(new Error('Outgoing message requires a valid address...'));
                }
                return;
            }

            // Copy message minus user & channel fields
            var bf_message = {};
            for (var key in message) {
                switch (key) {
                    case 'user':
                    case 'channel':
                        // ignore
                        break;
                    default:
                        bf_message[key] = message[key];
                        break;
                }
            }
            if (!bf_message.type) {
                bf_message.type = 'message';
            }

            // Ensure the message address has a valid conversation id.
            if (!bf_message.address.conversation) {
                bot.connector.startConversation(bf_message.address, function(err, adr) {
                    if (!err) {
                        // Send message through connector
                        bf_message.address = adr;
                        bot.connector.send([bf_message], done);
                    } else {
                        done(err);
                    }
                });
            } else {
                // Send message through connector
                bot.connector.send([bf_message], done);
            }
        };

        bot.reply = function(src, resp, cb) {
            var msg = {};

            if (typeof(resp) == 'string') {
                msg.text = resp;
            } else {
                msg = resp;
            }

            msg.user = src.user;
            msg.channel = src.channel;
            msg.address = src.address;

            bot.say(msg, cb);
        };

        bot.findConversation = function(message, cb) {
            botkit.debug('CUSTOM FIND CONVO', message.user, message.channel);
            for (var t = 0; t < botkit.tasks.length; t++) {
                for (var c = 0; c < botkit.tasks[t].convos.length; c++) {
                    if (
                        botkit.tasks[t].convos[c].isActive() &&
                        botkit.tasks[t].convos[c].source_message.user == message.user &&
                        botkit.tasks[t].convos[c].source_message.channel == message.channel
                    ) {
                        botkit.debug('FOUND EXISTING CONVO!');
                        cb(botkit.tasks[t].convos[c]);
                        return;
                    }
                }
            }

            cb();
        };

        // Create connector
        bot.connector = new builder.ChatConnector(config);

        return bot;

    });


    // set up a web route for receiving outgoing webhooks and/or slash commands

    bf_botkit.createWebhookEndpoints = function(webserver, bot, cb) {

        // Listen for incoming events
        bf_botkit.log(
            '** Serving webhook endpoints for the Microsoft Bot Framework at: ' +
                'http://' + bf_botkit.config.hostname + ':' +
                bf_botkit.config.port + '/botframework/receive');
        webserver.post('/botframework/receive', bot.connector.listen());

        // Receive events from chat connector
        bot.connector.onEvent(function(events, done) {
            for (var i = 0; i < events.length; i++) {
                // Break out user & chann ...
```
- example usage
```shell
...

To connect your bot to the Bot Framework follow the step by step guide outlined in [Getting Started](#getting-started).

Here is the complete code for a basic Bot Framework bot:

'''javascript
var Botkit = require('botkit');
var controller = Botkit.botframeworkbot({
});

var bot = controller.spawn({
        appId: process.env.app_id,
        appPassword: process.env.app_password
});
...
```

#### <a name="apidoc.element.botkit.consolebot"></a>[function <span class="apidocSignatureSpan">botkit.</span>consolebot (configuration)](#apidoc.element.botkit.consolebot)
- description and source-code
```javascript
function TextBot(configuration) {

    // Create a core botkit bot
    var text_botkit = Botkit(configuration || {});


    text_botkit.middleware.spawn.use(function(bot, next) {

        text_botkit.listenStdIn(bot);
        next();

    });

    text_botkit.defineBot(function(botkit, config) {

        var bot = {
            botkit: botkit,
            config: config || {},
            utterances: botkit.utterances,
        };

        bot.startConversation = function(message, cb) {
            botkit.startConversation(this, message, cb);
        };

        bot.send = function(message, cb) {
            console.log('BOT:', message.text);
        };

        bot.reply = function(src, resp, cb) {
            var msg = {};

            if (typeof(resp) == 'string') {
                msg.text = resp;
            } else {
                msg = resp;
            }

            msg.channel = src.channel;

            bot.say(msg, cb);
        };

        bot.findConversation = function(message, cb) {
            botkit.debug('CUSTOM FIND CONVO', message.user, message.channel);
            for (var t = 0; t < botkit.tasks.length; t++) {
                for (var c = 0; c < botkit.tasks[t].convos.length; c++) {
                    if (
                        botkit.tasks[t].convos[c].isActive() &&
                        botkit.tasks[t].convos[c].source_message.user == message.user
                    ) {
                        botkit.debug('FOUND EXISTING CONVO!');
                        cb(botkit.tasks[t].convos[c]);
                        return;
                    }
                }
            }

            cb();
        };

        return bot;

    });

    text_botkit.listenStdIn = function(bot) {

        text_botkit.startTicking();
        var rl = readline.createInterface({ input: process.stdin, output: process.stdout, terminal: false });
        rl.on('line', function(line) {
            var message = {
                text: line,
                user: 'user',
                channel: 'text',
                timestamp: Date.now()
            };
            text_botkit.receiveMessage(bot, message);
        });
    };

    return text_botkit;
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.botkit.core"></a>[function <span class="apidocSignatureSpan">botkit.</span>core (configuration)](#apidoc.element.botkit.core)
- description and source-code
```javascript
function Botkit(configuration) {
    var botkit = {
        events: {}, // this will hold event handlers
        config: {}, // this will hold the configuration
        tasks: [],
        taskCount: 0,
        convoCount: 0,
        my_version: null,
        my_user_agent: null,
        memory_store: {
            users: {},
            channels: {},
            teams: {},
        }
    };

    botkit.utterances = {
        yes: new RegExp(/^(yes|yea|yup|yep|ya|sure|ok|y|yeah|yah)/i),
        no: new RegExp(/^(no|nah|nope|n)/i),
        quit: new RegExp(/^(quit|cancel|end|stop|done|exit|nevermind|never mind)/i)
    };

    // define some middleware points where custom functions
    // can plug into key points of botkits process
    botkit.middleware = {
        send: ware(),
        receive: ware(),
        spawn: ware(),
        heard: ware(),
        capture: ware(),
    };


    function Conversation(task, message) {

        this.messages = [];
        this.sent = [];
        this.transcript = [];

        this.context = {
            user: message.user,
            channel: message.channel,
            bot: task.bot,
        };

        this.events = {};

        this.vars = {};

        this.threads = {};
        this.thread = null;

        this.status = 'new';
        this.task = task;
        this.source_message = message;
        this.handler = null;
        this.responses = {};
        this.capture_options = {};
        this.startTime = new Date();
        this.lastActive = new Date();

        this.collectResponse = function(key, value) {
            this.responses[key] = value;
        };

        this.capture = function(response, cb) {

            var that = this;
            var capture_key = this.sent[this.sent.length - 1].text;
            botkit.middleware.capture.run(that.task.bot, response, that, function(err, bot, response, convo) {
                if (response.text) {
                    response.text = response.text.trim();
                } else {
                    response.text = '';
                }

                if (that.capture_options.key != undefined) {
                    capture_key = that.capture_options.key;
                }

                // capture the question that was asked
                // if text is an array, get 1st
                if (typeof(that.sent[that.sent.length - 1].text) == 'string') {
                    response.question = that.sent[that.sent.length - 1].text;
                } else if (Array.isArray(that.sent[that.sent.length - 1].text)) {
                    response.question = that.sent[that.sent.length - 1].text[0];
                } else {
                    response.question = '';
                }

                if (that.capture_options.multiple) {
                    if (!that.responses[capture_key]) {
                        that.responses[capture_key] = [];
                    }
                    that.responses[capture_key].push(response);
                } else {
                    that.responses[capture_key] = response;
                }

                if (cb) cb(response);
            });

        };

        this.handle = function(message) {

            that = this;
            this.lastActive = new Date();
            this.transcript.push(message);
            botkit.debug('HANDLING MESSAGE IN CONVO', message);
            // do other stuff like call custom callbacks
            if (this.handler) {
                this.capture(message, function(message) {
                    // if the handler is a normal function, just execute it!
                    // NOTE: anyone who passes in their own handler has to call
                    // convo.next() to continue after completing whatever it is they want to do.
                    if (typeof(that.handler) == 'function') {
                        that.handler(message, that);
                    } else {
                        // handle might be a mapping of keyword to callback.
                        // lets see if the message matches any of the keywords
                        var match, p ...
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.botkit.facebookbot"></a>[function <span class="apidocSignatureSpan">botkit.</span>facebookbot (configuration)](#apidoc.element.botkit.facebookbot)
- description and source-code
```javascript
function Facebookbot(configuration) {

    var api_host = configuration.api_host || 'graph.facebook.com';

    // Create a core botkit bot
    var facebook_botkit = Botkit(configuration || {});

    if (facebook_botkit.config.require_delivery) {

        facebook_botkit.on('message_delivered', function(bot, message) {

            // get list of mids in this message
            for (var m = 0; m < message.delivery.mids.length; m++) {
                var mid = message.delivery.mids[m];

                // loop through all active conversations this bot is having
                // and mark messages in conversations as delivered = true
                bot.findConversation(message, function(convo) {
                    if (convo) {
                        for (var s = 0; s < convo.sent.length; s++) {
                            if (convo.sent[s].sent_timestamp <= message.delivery.watermark ||
                                (convo.sent[s].api_response && convo.sent[s].api_response.mid == mid)) {
                                convo.sent[s].delivered = true;
                            }
                        }
                    }
                });
            }

        });

    }

    // customize the bot definition, which will be used when new connections
    // spawn!
    facebook_botkit.defineBot(function(botkit, config) {

        var bot = {
            type: 'fb',
            botkit: botkit,
            config: config || {},
            utterances: botkit.utterances,
        };

        bot.startConversation = function(message, cb) {
            botkit.startConversation(this, message, cb);
        };

        bot.createConversation = function(message, cb) {
            botkit.createConversation(this, message, cb);
        };


        bot.send = function(message, cb) {

            var facebook_message = {
                recipient: {},
                message: message.sender_action ? undefined : {}
            };

            if (typeof(message.channel) == 'string' && message.channel.match(/\+\d+\(\d\d\d\)\d\d\d\-\d\d\d\d/)) {
                facebook_message.recipient.phone_number = message.channel;
            } else {
                facebook_message.recipient.id = message.channel;
            }

            if (!message.sender_action) {
                if (message.text) {
                    facebook_message.message.text = message.text;
                }

                if (message.attachment) {
                    facebook_message.message.attachment = message.attachment;
                }

                if (message.sticker_id) {
                    facebook_message.message.sticker_id = message.sticker_id;
                }

                if (message.quick_replies) {

                    // sanitize the length of the title to maximum of 20 chars
                    var titleLimit = function(title) {
                        if (title.length > 20) {
                            var newTitle = title.substring(0, 16) + '...';
                            return newTitle;
                        } else {
                            return title;
                        }
                    };

                    facebook_message.message.quick_replies = message.quick_replies.map(function(item) {
                        var quick_reply = {};
                        if (item.content_type === 'text' || !item.content_type) {
                            quick_reply = {
                                content_type: 'text',
                                title: titleLimit(item.title),
                                payload: item.payload,
                                image_url: item.image_url,
                            };
                        } else if (item.content_type === 'location') {
                            quick_reply = {
                                content_type: 'location'
                            };
                        } else {
                            // Future quick replies types
                        }
                        return quick_reply;
                    }); ...
```
- example usage
```shell
...

To connect your bot to Facebook, [follow the instructions here](https://developers.facebook.com/docs/messenger-platform/implementation
). You will need to collect your 'page token' as well as a 'verify token' that you define yourself and configure inside Facebook
's app settings. A step by step guide [can be found here](#getting-started). Since you must *already be running* your Botkit app
 to configure your Facebook app, there is a bit of back-and-forth. It's ok! You can do it.

Here is the complete code for a basic Facebook bot:

'''javascript
var Botkit = require('botkit');
var controller = Botkit.facebookbot({
        access_token: process.env.access_token,
        verify_token: process.env.verify_token,
})

var bot = controller.spawn({
});
...
```

#### <a name="apidoc.element.botkit.slackbot"></a>[function <span class="apidocSignatureSpan">botkit.</span>slackbot (configuration)](#apidoc.element.botkit.slackbot)
- description and source-code
```javascript
function Slackbot(configuration) {

    // Create a core botkit bot
    var slack_botkit = Botkit(configuration || {});

    // Set some default configurations unless they've already been set.

    // Should the RTM connections ingest received messages
    // Developers using the new Events API will set this to false
    // This allows an RTM connection to be kept alive (so bot appears online)
    // but receive messages only via events api
    if (slack_botkit.config.rtm_receive_messages === undefined) {
        slack_botkit.config.rtm_receive_messages = true;
    }

    var spawned_bots = [];

    // customize the bot definition, which will be used when new connections
    // spawn!
    slack_botkit.defineBot(require(__dirname + '/Slackbot_worker.js'));

    // Middleware to track spawned bots and connect existing RTM bots to incoming webhooks
    slack_botkit.middleware.spawn.use(function(worker, next) {

        // lets first check and make sure we don't already have a bot
        // for this team! If we already have an RTM connection, copy it
        // into the new bot so it can be used for replies.

        var existing_bot = null;
        if (worker.config.id) {
            for (var b = 0; b < spawned_bots.length; b++) {
                if (spawned_bots[b].config.id) {
                    if (spawned_bots[b].config.id == worker.config.id) {
                        // WAIT! We already have a bot spawned here.
                        // so instead of using the new one, use the exist one.
                        existing_bot = spawned_bots[b];
                    }
                }
            }
        }

        if (!existing_bot && worker.config.id) {
            spawned_bots.push(worker);
        } else if (existing_bot) {
            if (existing_bot.rtm) {
                worker.rtm = existing_bot.rtm;
            }
        }
        next();

    });



    // set up configuration for oauth
    // slack_app_config should contain
    // { clientId, clientSecret, scopes}
    // https://api.slack.com/docs/oauth-scopes
    slack_botkit.configureSlackApp = function(slack_app_config, cb) {

        slack_botkit.log('** Configuring app as a Slack App!');
        if (!slack_app_config || !slack_app_config.clientId ||
            !slack_app_config.clientSecret || !slack_app_config.scopes) {
            throw new Error('Missing oauth config details');
        } else {
            slack_botkit.config.clientId = slack_app_config.clientId;
            slack_botkit.config.clientSecret = slack_app_config.clientSecret;
            if (slack_app_config.redirectUri) slack_botkit.config.redirectUri = slack_app_config.redirectUri;
            if (typeof(slack_app_config.scopes) == 'string') {
                slack_botkit.config.scopes = slack_app_config.scopes.split(/\,/);
            } else {
                slack_botkit.config.scopes = slack_app_config.scopes;
            }
            if (cb) cb(null);
        }

        return slack_botkit;

    };

    // set up a web route that is a landing page
    slack_botkit.createHomepageEndpoint = function(webserver) {

        slack_botkit.log('** Serving app landing page at : http://' +
            slack_botkit.config.hostname + ':' + slack_botkit.config.port + '/');

        // FIX THIS!!!
        // this is obvs not right.
        webserver.get('/', function(req, res) {

            res.send('Howdy!');

        });

        return slack_botkit;

    };


    // adds the webhook authentication middleware module to the webserver
    function secureWebhookEndpoints() {
        var authenticationMiddleware = require(__dirname + '/middleware/slack_authentication.js');
        // convert a variable argument list to an array, drop the webserver argument
        var tokens = Array.prototype.slice.call(arguments);
        var webserver = tokens.shift();

        slack_botkit.log(
            '** Requiring token authentication for webhook endpoints for Slash commands ' +
            'and outgoing webhooks; configured ' + tokens.length + ' token(s)'
        );

        webserver.use(authent ...
```
- example usage
```shell
...
Then pass the returned storage when creating your Botkit controller. Botkit will do the rest.

Make sure everything you store has an 'id' property, that's what you'll use to look it up later.

'''javascript
var Botkit = require('botkit'),
    mongoStorage = require('botkit-storage-mongo')({mongoUri: '...'}),
    controller = Botkit.slackbot({
        storage: mongoStorage
    });
'''

'''javascript
// then you can use the Botkit storage api, make sure you have an id property
var beans = {id: 'cool', beans: ['pinto', 'garbanzo']};
...
```

#### <a name="apidoc.element.botkit.sparkbot"></a>[function <span class="apidocSignatureSpan">botkit.</span>sparkbot (configuration)](#apidoc.element.botkit.sparkbot)
- description and source-code
```javascript
function Sparkbot(configuration) {

    // Create a core botkit bot
    var controller = Botkit(configuration || {});

    if (!controller.config.ciscospark_access_token) {
        throw new Error('ciscospark_access_token required to create controller');
    } else {
        controller.api = require('ciscospark').init({
            credentials: {
                authorization: {
                    access_token: controller.config.ciscospark_access_token
                }
            }
        });

        if (!controller.api) {
            throw new Error('Could not create Cisco Spark API');
        }

        controller.api.people.get('me').then(function(identity) {
            console.log('Cisco Spark: My identity is', identity);
            controller.identity = identity;
        }).catch(function(err) {
            throw new Error(err);
        });
    }

    if (!controller.config.public_address) {
        throw new Error('public_address parameter required to receive webhooks');
    } else {

        var endpoint = url.parse(controller.config.public_address);
        if (!endpoint.hostname) {
            throw new Error('Could not determine hostname of public address: ' + controller.config.public_address);
        } else if (endpoint.protocol != 'https:') {
            throw new Error('Please specify an SSL-enabled url for your public address: ' + controller.config.public_address);
        } else {
            controller.config.public_address = endpoint.hostname + (endpoint.port ? ':' + endpoint.port : '');
        }

    }

    if (!controller.config.secret) {
        console.log('WARNING: No secret specified. Source of incoming webhooks will not be validated. https://developer.ciscospark
.com/webhooks-explained.html#auth');
        // throw new Error('secret parameter required to secure webhooks');
    }


    controller.resetWebhookSubscriptions = function() {
        controller.api.webhooks.list().then(function(list) {
            for (var i = 0; i < list.items.length; i++) {
                controller.api.webhooks.remove(list.items[i]).then(function(res) {
                    console.log('Removed subscription: ' + list.items[i].name);
                }).catch(function(err) {
                    console.log('Error removing subscription:', err);
                });
            }
        });
    };

    // set up a web route for receiving outgoing webhooks and/or slash commands
    controller.createWebhookEndpoints = function(webserver, bot, cb) {


        var webhook_name = controller.config.webhook_name || 'Botkit Firehose';

        controller.log(
            '** Serving webhook endpoints for Cisco Spark Platform at: ' +
            'http://' + controller.config.hostname + ':' + controller.config.port + '/ciscospark/receive');
        webserver.post('/ciscospark/receive', function(req, res) {

            controller.handleWebhookPayload(req, res, bot);

        });


        var list = controller.api.webhooks.list().then(function(list) {
            var hook_id = null;

            for (var i = 0; i < list.items.length; i++) {
                if (list.items[i].name == webhook_name) {
                    hook_id = list.items[i].id;
                }
            }

            var hook_url = 'https://' + controller.config.public_address + '/ciscospark/receive';

            console.log('Cisco Spark: incoming webhook url is ', hook_url);

            if (hook_id) {
                controller.api.webhooks.update({
                    id: hook_id,
                    resource: 'all',
                    targetUrl: hook_url,
                    event: 'all',
                    secret: controller.config.secret,
                    name: webhook_name,
                }).then(function(res) {
                    console.log('Cisco Spark: SUCCESSFULLY UPDATED CISCO SPARK WEBHOOKS');
                    if (cb) cb();
                }).catch(function(err) {
                    console.log('FAILED TO REGISTER WEBHOOK', err);
                    throw new Error(err);
                });

            } else { ...
```
- example usage
```shell
...
This will configure your bot to respond only to messages from members of the specific organization, or whose email addresses match
 one of the specified domains.

The full code for a simple Cisco Spark bot is below:

'''
var Botkit = require('./lib/Botkit.js');

var controller = Botkit.sparkbot({
    debug: true,
    log: true,
    public_address: process.env.public_address,
    ciscospark_access_token: process.env.access_token,
    secret: process.env.secret
});
...
```

#### <a name="apidoc.element.botkit.twilioipmbot"></a>[function <span class="apidocSignatureSpan">botkit.</span>twilioipmbot (configuration)](#apidoc.element.botkit.twilioipmbot)
- description and source-code
```javascript
function Twiliobot(configuration) {

    // Create a core botkit bot
    var twilio_botkit = Botkit(configuration || {});

    // customize the bot definition, which will be used when new connections
    // spawn!
    twilio_botkit.defineBot(function(botkit, config) {
        var bot = {
            type: 'twilioipm',
            botkit: botkit,
            config: config || {},
            utterances: botkit.utterances,
        };

        bot.startConversation = function(message, cb) {
            botkit.startConversation(this, message, cb);
        };

        bot.send = function(message, cb) {
            botkit.debug('SEND ', message);

            if (bot.identity === null || bot.identity === '') {
                bot.api.channels(message.channel).messages.create({
                    body: message.text,
                }).then(function(response) {
                    cb(null, response);
                }).catch(function(err) {
                    cb(err);
                });
            } else {
                bot.api.channels(message.channel).messages.create({
                    body: message.text,
                    from: bot.identity
                }).then(function(response) {
                    cb(null, response);
                }).catch(function(err) {
                    cb(err);
                });
            }
        };

        bot.reply = function(src, resp, cb) {
            var msg = {};

            if (typeof(resp) == 'string') {
                msg.text = resp;
            } else {
                msg = resp;
            }

            msg.user = src.user;
            msg.channel = src.channel;

            bot.say(msg, cb);
        };

        bot.autoJoinChannels = function() {
          bot.api.channels.list().then(function(full_channel_list) {
            if (bot.config.autojoin === true) {
                bot.channels = full_channel_list;
                bot.channels.channels.forEach(function(chan) {
                    bot.api.channels(chan.sid).members.create({
                        identity: bot.identity
                    }).then(function(response) {
                        botkit.debug('added ' +
                          bot.identity + ' as a member of the ' + chan.friendly_name);
                    }).fail(function(error) {
                        botkit.debug('Couldn\'t join the channel: ' +
                            chan.friendly_name + ': ' + error);
                    });
                });
            } else if (bot.identity) {

                // load up a list of all the channels that the bot is currently

                bot.channels = {
                    channels: []
                };

                async.each(full_channel_list.channels, function(chan, next) {
                    bot.api.channels(chan.sid).members.list().then(function(members) {
                        for (var x = 0; x < members.members.length; x++) {
                            if (members.members[x].identity == bot.identity) {
                                bot.channels.channels.push(chan);
                            }
                        }
                        next();
                    }).fail(function(error) {
                        botkit.log('Error loading channel member list: ', error);
                        next();
                    });
                });
            }
        }).fail(function(error) {
            botkit.log('Error loading channel list: ' + error);
            // fails if no channels exist
            // set the channels to empty
            bot.channels = { channels: [] };
        });

      };

        bot.configureBotIdentity = function() {
            if (bot.identity !== null || bot.identity !== '') {
                var userRespIter = 0;
                var existingIdentity = null;

                // try the get by identity thing
                bot.api.users(bot.identity).get().then(function(response) {
                    bot.autoJoinChannels();
                }).fail(function(error) {
                    // if not make the new user and se ...
```
- example usage
```shell
...

To connect your bot to Twilio, [follow the instructions here](https://www.twilio.com/user/account/ip-messaging/getting-started).
You will need to collect 5 separate pieces of your API credentials. A step by step guide [can be found here](#getting-started).
Since you must *already be running* your Botkit app to fully configure your Twilio app, there is a bit of back-and-forth. It's ok
! You can do it.

Here is the complete code for a basic Twilio bot:

'''javascript
var Botkit = require('botkit');
var controller = Botkit.twilioipmbot({
debug: false
})

var bot = controller.spawn({
TWILIO_IPM_SERVICE_SID: process.env.TWILIO_IPM_SERVICE_SID,
TWILIO_ACCOUNT_SID: process.env.TWILIO_ACCOUNT_SID,
TWILIO_API_KEY: process.env.TWILIO_API_KEY,
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
