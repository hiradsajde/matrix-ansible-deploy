# Setting up Mautrix Hangouts bridging (optional, deprecated)

**Note**: This bridge has been deprecated in favor of [Google Chat bridge](https://github.com/mautrix/googlechat), which can be installed using [this playbook](configuring-playbook-bridge-mautrix-googlechat.md). Consider using that bridge instead of this one.

The playbook can install and configure [mautrix-hangouts](https://github.com/mautrix/hangouts) for you.

See the project's [documentation](https://docs.mau.fi/bridges/python/hangouts/index.html) to learn what it does and why it might be useful to you.

## Prerequisite (optional)

If you want to set up [Double Puppeting](https://docs.mau.fi/bridges/general/double-puppeting.html) (hint: you most likely do) for this bridge automatically, you need to have enabled [Shared Secret Auth](configuring-playbook-shared-secret-auth.md) for this playbook.

For details about configuring Double Puppeting for this bridge, see the section below: [Set up Double Puppeting](#-set-up-double-puppeting)

## Adjusting the playbook configuration

To enable the [Google Hangouts](https://hangouts.google.com/) bridge, add the following configuration to your `inventory/host_vars/matrix.example.com/vars.yml` file:

```yaml
matrix_mautrix_hangouts_enabled: true
```

## Installing

After configuring the playbook, run it with [playbook tags](playbook-tags.md) as below:

<!-- NOTE: let this conservative command run (instead of install-all) to make it clear that failure of the command means something is clearly broken. -->
```sh
ansible-playbook -i inventory/hosts setup.yml --tags=setup-all,ensure-matrix-users-created,start
```

**Notes**:

- The `ensure-matrix-users-created` playbook tag makes the playbook automatically create the bot's user account.

- The shortcut commands with the [`just` program](just.md) are also available: `just install-all` or `just setup-all`

  `just install-all` is useful for maintaining your setup quickly ([2x-5x faster](../CHANGELOG.md#2x-5x-performance-improvements-in-playbook-runtime) than `just setup-all`) when its components remain unchanged. If you adjust your `vars.yml` to remove other components, you'd need to run `just setup-all`, or these components will still remain installed.

## Usage

Once the bot is enabled you need to start a chat with `Hangouts bridge bot` with handle `@hangoutsbot:example.com` (where `example.com` is your base domain, not the `matrix.` domain).

Send `login` to the bridge bot to receive a link to the portal from which you can enable the bridging. Open the link sent by the bot and follow the instructions.

Automatic login may not work. If it does not, reload the page and select the "Manual login" checkbox before starting. Manual login involves logging into your Google account normally and then manually getting the OAuth token from browser cookies with developer tools.

Once logged in, recent chats should show up as new conversations automatically. Other chats will get portals as you receive messages.

You can learn more about authentication from the bridge's [official documentation on Authentication](https://docs.mau.fi/bridges/python/hangouts/authentication.html).

### 💡 Set up Double Puppeting

After successfully enabling bridging, you may wish to set up [Double Puppeting](https://docs.mau.fi/bridges/general/double-puppeting.html) (hint: you most likely do).

To set it up, you have 2 ways of going about it.

#### Method 1: automatically, by enabling Shared Secret Auth

The bridge automatically performs Double Puppeting if [Shared Secret Auth](configuring-playbook-shared-secret-auth.md) service is configured and enabled on the server for this playbook.

This is the recommended way of setting up Double Puppeting, as it's easier to accomplish, works for all your users automatically, and has less of a chance of breaking in the future.

#### Method 2: manually, by asking each user to provide a working access token

When using this method, **each user** that wishes to enable Double Puppeting needs to follow the following steps:

- retrieve a Matrix access token for yourself. Refer to the documentation on [how to do that](obtaining-access-tokens.md).

- send the access token to the bot. Example: `login-matrix MATRIX_ACCESS_TOKEN_HERE`

- make sure you don't log out the `Mautrix-Hangouts` device some time in the future, as that would break the Double Puppeting feature
