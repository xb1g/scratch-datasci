# Contribute to Scratch Data Science

- **Setup your development environment**
  - [Set up repository](#set-up-your-repository)
- **Create your extension**
  - [Create a block using live data from an online API](#create-a-block-using-live-data)
  - [Create a block using a JavaScript module from npm](#create-a-block-using-a-javascript-module)
  - [Customize the Extension Menu](#customize-the-extensions-menu)
- **Publish your extension**
  - [Publish your Scratch extension](#publish-your-finished-extension)
  - [Stop your codespace](#stop-your-codespace)

---

---

## Set up your repository

Clone this repository to your local machine.

```sh
git clone https://github.com/CoderDojoThailand/DataSciBlock.git
cd DataSciBlock
```

In the terminal at the bottom of the window, run:

```sh
./0-setup.sh
```

![screenshot](./docs/30-setup.png)

This should be very quick.

You only need to do this once (but it is safe if you run it again).

![screenshot](./docs/31-setup-complete.png)

---

## Code Structure

Most of the code are in `scratch3_datasci/index.js` file.

The `getInfo()` function contains the metadata for the extension.

```js
getInfo () {
  return {
    // unique ID for your extension. is used to call the extension from the manager in the vm
    id: 'datasci',

    // name displayed in the Scratch UI
    name: 'Demo',

    // colours to use for your extension blocks
    color1: '#000099',
    color2: '#660066',

    // your Scratch blocks
    blocks: [
      {
        // function where your code logic lives
        opcode: 'myFirstBlock',

        // type of block
        blockType: BlockType.REPORTER,

        // label to display on the block
        text: 'Title for ISBN book [BOOK_NUMBER]',

        // arguments used in the block
        arguments: {
          BOOK_NUMBER: {
            defaultValue: 1718500564,

            // type/shape of the parameter
            type: ArgumentType.NUMBER
          }
        }
      }
    ]
  };
}
```

![screenshot](docs/42-getinfo.png)

Edit the `myFirstBlock` function implementation to look up book info using the [OpenLibrary API](https://openlibrary.org/dev/docs/api/books).

```js
myFirstBlock ({ BOOK_NUMBER }) {
  return fetch('https://openlibrary.org/isbn/' + BOOK_NUMBER + '.json')
    .then((response) => {
      if (response.ok) {
        return response.json();
      }
      else {
        return { title: 'Unknown' };
      }
    })
    .then((bookinfo) => {
      return bookinfo.title;
    });
}
```

![screenshot](./docs/43-firstblock.png)

Your code is now ready to test.

---

## Launch a private test of your Scratch extension

In the terminal at the bottom of the window, run:

```sh
./2-build.sh
```

![screenshot](./docs/50-build.png)

This can take a minute to run. Wait for this to complete.

In the terminal at the bottom of the window, run:

```sh
./3-run-private.sh
```

![screenshot](./docs/52-run.png)

A pop-up should appear in the bottom-right with a button to open a private window with your modified version of Scratch.

![screenshot](./docs/53-running.png)

If it doesn't appear, or you accidentally dismiss it, you can get the link from the **Open in browser** button on the **Ports** tab.

![screenshot](./docs/54-open-in-browser.png)

Either way, click on **Open in browser**.

![screenshot](./docs/55-private-scratch.png)

This is a private copy of Scratch that only you can access. You can use this to test your new extension.

Click on the **Extensions** button.

![screenshot](./docs/55-extensions-button.png)

You should see your extension added to the menu. Click on it.

![screenshot](./docs/55-extensions-menu.png)

Make a simple Scratch project using your extension.

![screenshot](./docs/56-testing.png)

If you need to make a change, stop your Scratch test by pressing **Control-C** in the terminal.

Make your code changes.

Then re-build and test again by typing:

```sh
./2-build.sh
./3-run-private.sh
```

Once you have finished, stop your Scratch test by pressing **Control-C** in the terminal.

![screenshot](./docs/57-stop-test.png)

---

## Create a block using a JavaScript module

**The instructions here will show you how to write an extension that can estimate the number of syllables in some English text, using a JavaScript module.**

The instructions will go through the template JavaScript one section at a time.

Select a module from https://www.npmjs.com - for this example, I'm using `syllable`.

![screenshot](./docs/80-npmjs.png)

Run `./1-add-dependency.sh` with the name of the module you've selected.

```sh
./1-add-dependency.sh syllable
```

![screenshot](./docs/81-add-dependency.png)

As long as you have spelled it exactly correctly, it will update the dependencies for your private Scratch build to include the new module.

_If you want to add multiple dependencies, you can run this script multiple times. Running the script with the name of a dependency you have already added is safe, but not necessary._

![screenshot](./docs/82-npm-install-complete.png)

Open the `your-scratch-extension/index.js` file again.

Edit the `constructor` function to load the module.

```js
constructor (runtime) {
  import('syllable')
    .then((syllableModule) => {
      this.syllable = syllableModule.syllable;
    });
}
```

![screenshot](./docs/83-constructor.png)

Edit the `getInfo()` function to add a second block, after the block we defined before.

The complete `getInfo()` function will now contain:

```js
getInfo () {
  return {
    // unique ID for your extension
    id: 'yourScratchExtension',

    // name displayed in the Scratch UI
    name: 'Demo',

    // colours to use for your extension blocks
    color1: '#000099',
    color2: '#660066',

    // your Scratch blocks
    blocks: [
      {
        // function where your code logic lives
        opcode: 'myFirstBlock',

        // type of block
        blockType: BlockType.REPORTER,

        // label to display on the block
        text: 'Title for ISBN book [BOOK_NUMBER]',

        // arguments used in the block
        arguments: {
          BOOK_NUMBER: {
            defaultValue: 1718500564,

            // type/shape of the parameter
            type: ArgumentType.NUMBER
          }
        }
      },
      {
        // function where your code logic lives
        opcode: 'mySecondBlock',

        // type of block
        blockType: BlockType.REPORTER,

        // label to display on the block
        text: 'Syllables in [MY_TEXT]',

        // arguments used in the block
        arguments: {
          MY_TEXT: {
            defaultValue: 'Hello World',

            // type/shape of the parameter
            type: ArgumentType.STRING
          }
        }
      }
    ]
  };
}
```

![screenshot](./docs/84-get-info.png)

Add a `mySecondBlock` function implementation to return a count of syllables using the loaded npm module.

```js
mySecondBlock ({ MY_TEXT }) {
  return this.syllable(MY_TEXT);
}
```

![screenshot](./docs/85-firstblock.png)

Your code is now ready to test.

As before, build your code:

```sh
./2-build.sh
```

![screenshot](./docs/86-build.png)

Then run a private instance of Scratch to test it.

```sh
./3-run-private.sh
```

![screenshot](./docs/86-private-test.png)

When prompted, click on the **Open in browser** button to open your private Scratch instance.

![screenshot](./docs/86-open.png)

You can make a simple Scratch script to verify that your new block is working.

![screenshot](./docs/87-test.png)

When you've finished your test, close the Scratch window, and then stop the test instance by pressing **Control-C** in the Terminal.

![screenshot](./docs/88-stop.png)

---

## Customize the Extensions menu

The extensions menu includes images to represent each extension. Each extension has a large background image, and a small inset icon.

Placeholder images are provided for your extension.

![screenshot](./docs/58-extensions.png)

If you're happy with these, you can [skip to the next step](#publish-your-finished-extension).

If you want to customize these, you can edit the image files `your-extension-background.png` and `your-extension-icon.png` to better represent your Scratch extension.

![screenshot](./docs/59-extensions-icons.png)

I recommend keeping the dimensions of the images the same as they currently are to best fit in the menu.

Note that you will need to rebuild your extension after making changes to these files.

```sh
./2-build.sh
```

And to see the new menu in action you will want to start your private test instance again.

```sh
./3-run-private.sh
```

Before proceeding to the next step, make sure you have stopped your private test instance of Scratch by pressing **Control-C** in the terminal.

![screenshot](./docs/57-stop-test.png)

---

## Publish your finished extension

In the terminal at the bottom of the window, run:

```sh
./4-publish.sh
```

![screenshot](./docs/60-publish.png)

This can take a minute to run.

![screenshot](./docs/61-published.png)

Your Scratch fork will be live and publicly accessible at:

```
https://<YOUR-GITHUB-USERNAME>.github.io/<YOUR-REPOSITORY-NAME>/scratch
```

For example, in my screenshots I created a GitHub account with the username `scratch-extensions-demo` and when I forked the repository, I named it `my-demo`.

![screenshot](./docs/64-public-url.png)

_Note that this can sometimes take a minute to go live, so if the link doesn't work, it's worth waiting a minute and trying again. (But if it still doesn't work, check you have got the URL correct!)_

![screenshot](./docs/62-live.png)

You can give this URL to your students.

![screenshot](./docs/63-testing.png)

---

## Stop your codespace

You only need your codespace running while you are developing your extension. Once it's published, you must stop your codespace.

On your repository page, click on **Code** -> **Codespaces** -> **Stop codespace**.

![screenshot](./docs/70-stop.png)

Your published Scratch instance, with your extensions, will still be accessible after you do this.

![screenshot](./docs/63-testing.png)

---
