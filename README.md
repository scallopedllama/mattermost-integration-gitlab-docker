# Docker image for `mattermost-integration-gitlab`

This Dockerfile provides an Ubuntu 14.04 instance ready to run the
[`mattermost-integration-gitlab` project from NotSqrt](https://github.com/NotSqrt/mattermost-integration-gitlab).

The image contains the following:

* Ubuntu 14.04
* Python 2
* Python-dev and build-essential
* Python PIP
* `mattermost-integration-gitlab`

The default container CMD is to simply run the `mattermost_gitlab` script with `${MATTERMOST_WEBHOOK_URL}`
passed through from the Docker command environment settings.

## Using the container

### Configure Mattermost to receive an incoming webhook from the plugin

1. Enter the Mattermost System Console and choose "Custom Integrations"
2. Make sure that Incoming Webcooks are enabled
3. If desired, create a new channel into which GitLab messages should be sent
4. Return to your Mattermost Project and click the "..." menu next to your name in the side bar
5. Choose "Integrations"
6. Click "Incoming Webhooks"
7. Add new Incoming Webhook
8. Provide it with a name and description and choose a channel into which the GitLab messages will be sent then click Save
9. Copy the resulting URL, this is the `MATTERMOST_WEBHOOK_URL`

### Start the `mattermost-integration-gitlab` container

On your system that already has Docker configured, run the container, defining a name,
the `MATTERMOST_WEBHOOK_URL` in the environment, and exposing the port needed to talk to the server:

    docker run --detach --restart=always --name mattermost-integration-gitlab -e "MATTERMOST_WEBHOOK_URL=http://mattermost.example.io/hooks/blahblahblahkeyhere" -p 5000:5000 scallopedllama/mattermost-integration-gitlab

Make sure to replace `http://mattermost.example.io/hooks/blahblahblahkeyhere` with the `MATTERMOST_WEBHOOK_URL` obtained above.
This will start the `mattermost-integration-gitlab` server in a container in the background (`--detach`),
and configure the container to automatically be restarted on reboot (`--restart=always`).

### Configure GitLab Webhooks

1. Log into GitLab and open the project that you would to have changes about posted to Mattermost. You will have to do the following steps for all projects for which you would like notifications.
2. Click on Settings (⚙ in the top right) > Webhooks
3. In the URL field, enter the url to the `mattermost-integration-gitlab` container launched above. This can be an IP but make sure to specify `http://` and the port, then add `/new_event` to the end of this URL.  
   `http://192.168.1.123:5000/new_event`
4. Under Trigger, select Push Events, Comment Events, Issue Events, and Merge Request Events
6. Check Enable SSL Verification
5. Click Add Webhook
6. Scroll to the bottom of the page and confirm your Webhook is there, click the Test button and make sure it works

Your project should now be set up and work.
