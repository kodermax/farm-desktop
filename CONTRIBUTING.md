# Contributor Guidelines

## Advice for new contributors

Start small. The PRs most likely to be merged are the ones that make small,
easily reviewed changes with clear and specific intentions. See below for more
[guidelines on pull requests](#pull-requests).

It's a good idea to gauge interest in your intended work by finding the current issue
for it or creating a new one yourself. You can use also that issue as a place to signal
your intentions and get feedback from the users most likely to appreciate your changes.
If you intend to make a pull request for a new feature and the feature is user facing,
please let us know in a new, or existing issue, so we can guide your efforts around 
UX/UI considerations.

You're most likely to have your pull request accepted easily if it addresses bugs already
in the [Next Steps project](https://github.com/oxen-io/session-desktop/projects/1),
especially if they are near the top of the Backlog column. Those are what we'll be looking
at next, so it would be great if you helped us out!

Once you've spent a little bit of time planning your solution, it's a good idea to go
back to the issue and talk about your approach. We'd be happy to provide feedback. [An
ounce of prevention, as they say!](https://www.goodreads.com/quotes/247269-an-ounce-of-prevention-is-worth-a-pound-of-cure)

## Developer Setup

First, you'll need [Node.js](https://nodejs.org/) which matches our current version.
You can check [`.nvmrc` in the `clearnet` branch](https://github.com/oxen-io/session-desktop/blob/clearnet/.nvmrc) to see what the current version is. If you have [nvm](https://github.com/creationix/nvm)
you can just run `nvm use` in the project directory and it will switch to the project's
desired Node.js version. [nvm for windows](https://github.com/coreybutler/nvm-windows) is
still useful, but it doesn't support `.nvmrc` files.

Then you need `git`, if you don't have that yet: https://git-scm.com/

### macOS

1.  Install the [Xcode Command-Line Tools](http://osxdaily.com/2014/02/12/install-command-line-tools-mac-os-x/).

### Windows

1.  **Windows 7 only:**
    - Install Microsoft .NET Framework 4.5.1:
      https://www.microsoft.com/en-us/download/details.aspx?id=40773
    - Install Windows SDK version 8.1: https://developer.microsoft.com/en-us/windows/downloads/sdk-archive
1.  Install _Windows Build Tools_: Open the [Command Prompt (`cmd.exe`) as Administrator](<https://technet.microsoft.com/en-us/library/cc947813(v=ws.10).aspx>)
    and run: `npm install --vs2015 --global --production --add-python-to-path windows-build-tools`

### Linux

1.  Pick your favorite package manager.
1.  Install `python`
1.  Install `gcc`
1.  Install `g++`
1.  Install `make`
1.  Depending on your distro, you might need to install `hunspell` and `hunspell-<lan>` (e.g. `hunspell-en-au`)

### All platforms

Now, run these commands in your preferred terminal in a good directory for development:

```
git clone https://github.com/oxen-io/session-desktop.git
cd session-desktop
npm install --global yarn      # (only if you don’t already have `yarn`)
yarn install --frozen-lockfile # Install and build dependencies (this will take a while)
yarn build-everything
yarn test                      # A good idea to make sure tests run first
yarn start-prod                # Start Session!
```

You'll need to restart the application regularly to see your changes, as there
is no automatic restart mechanism. Alternatively, keep the developer tools open
(`View > Toggle Developer Tools`), hover over them, and press
<kbd>Cmd</kbd> + <kbd>R</kbd> (macOS) or <kbd>Ctrl</kbd> + <kbd>R</kbd>
(Windows & Linux).

```
yarn build-everything:watch # runs until you stop it, re-generating built assets on file changes
# Once this command is waiting for changes, you will need to run in another terminal `yarn parcel-util-worker` to fix the "exports undefined" error on start.
# If you do change the sass while this command is running, it won't pick it up. You need to either run `yarn sass` or have `yarn sass:watch` running in a separate terminal.
```

## Multiple instances

Since there is no registration for Session, you can create as many accounts as you
can public keys. Each client however has a dedicated storage profile which is determined by the environment and instance variables.

This profile will change [userData](https://electron.atom.io/docs/all/#appgetpathname)
directory from `%appData%/Session` to `%appData%/Session-{environment}-{instance}`.

There are a few scripts which you can use:

```
yarn start-prod - Start production but in development mode
MULTI=1 yarn start-prod - Start another instance of production
```

For more than 2 clients, you may run the above command with `NODE_APP_INSTANCE` set before them.
For example, running:

```
NODE_APP_INSTANCE=alice yarn start-prod
```

Will run the development environment with the `alice` instance and thus create a seperate storage profile.

If a fixed profile is needed (in the case of tests), you can specify it using `storageProfile` in the config file. If the change is local then put it in `local-{instance}.json` otherwise put it in `default-{instance}.json` or `{env}-{instance}.json`.

Local config files will be ignored by default in git.

For example, to create an 'alice' profile locally, put a file called `local-alice.json` in the
`config` directory:

```
{
  "storageProfile": "alice-profile",
}
```

This will then set the `userData` directory to `%appData%/Session-alice-profile` when running the `alice` instance.

# Making changes

So you're in the process of preparing that pull request. Here's how to make that go
smoothly.

## Tests

Please write tests! Our testing framework is
[mocha](http://mochajs.org/) and our assertion library is
[chai](http://chaijs.com/api/assert/).

The easiest way to run all tests at once is `yarn test`.

## Pull requests

So you wanna make a pull request? Please observe the following guidelines.

<!-- TODO:
* Please do not submit pull requests for translation fixes. Anyone can update
  the translations in
  [Transifex](https://www.transifex.com/projects/p/signal-desktop).
-->

- First, make sure that your `yarn ready` run passes - it's very similar to what our
  Continuous Integration servers do to test the app.
- Never use plain strings right in the source code - pull them from `messages.json`!
  You **only** need to modify the default locale
  [`_locales/en/messages.json`](_locales/en/messages.json).
  <!-- TODO:
    Other locales are generated automatically based on that file and then periodically
    uploaded to Transifex for translation. -->
- [Rebase](https://nathanleclaire.com/blog/2014/09/14/dont-be-scared-of-git-rebase/) your
  changes on the latest `development` branch, resolving any conflicts.
  This ensures that your changes will merge cleanly when you open your PR.
- Be sure to add and run tests!
- Make sure the diff between our master and your branch contains only the
  minimal set of changes needed to implement your feature or bugfix. This will
  make it easier for the person reviewing your code to approve the changes.
  Please do not submit a PR with commented out code or unfinished features.
- Avoid meaningless or too-granular commits. If your branch contains commits like
  the lines of "Oops, reverted this change" or "Just experimenting, will
  delete this later", please [squash or rebase those changes away](https://robots.thoughtbot.com/git-interactive-rebase-squash-amend-rewriting-history).
- Don't have too few commits. If you have a complicated or long lived feature
  branch, it may make sense to break the changes up into logical atomic chunks
  to aid in the review process.
- Provide a well written and nicely formatted commit message. See [this
  link](http://chris.beams.io/posts/git-commit/)
  for some tips on formatting. As far as content, try to include in your
  summary
  1.  What you changed
  2.  Why this change was made (including git issue # if appropriate)
  3.  Any relevant technical details or motivations for your implementation
      choices that may be helpful to someone reviewing or auditing the commit
      history in the future. When in doubt, err on the side of a longer
      commit message.

Above all, spend some time with the repository. Follow the pull request template added to
your pull request description automatically. Take a look at recent approved pull requests,
see how they did things.

## Production Builds

You can build a production binary by running the following:

```
yarn build-everything
yarn build-release
```
