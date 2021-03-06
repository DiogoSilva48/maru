![Maru Logo](https://cloudflare-ipfs.com/ipfs/QmYWGWmauYZk8vBsYhEnqYtZjC41ajv4ojUQH3iSFCa6Mp)
# Project Maru (Working Title)

Project Maru is a Chromium OS distribution focusing on upstream support, while providing a secure and solid OS base for everyone, ranging from Linux enthusiasts to developers.

Like Chromium OS, it's straightforward, with an adequate amount of bells and whistles to get you going which is a solid browser experience.

## The name

The name "Maru" is from Hanamaru Kunikida of Love Live Sunshine.


## Disclaimer

We're not associated with [MaruOS](https://maruos.com), and Maru is simply a working title while we're still asking the small community we have which should we name it later on.

## Roadmap

- [x] :clap: MAKE :clap: IT :clap: BUILD :clap:

- [x] work on driver support.
   - [x] include Libinput support and add additional drivers.

- [ ] Omaha Update server
  - This is intended for OTA updates. We want people to use Maru as a simple update.

- [ ] Other architecture support
  - [ ] ARM64
      - May Range from Raspberry Pi 4, Udoo, etc.


## Building Maru

You'll need to grab the entire Chromium OS Source code to build this.

### The Easy way

Run `unibuild.sh` with the architecture of choice. Currently only `amd64` and `aarch64` are supported.

### The Hard way

If that doesn't work, you'll need to read on to build manually:

#### Fetching `depot_tools` 

To be able to pull the entire Chromium OS tree, we'll need to use Google's in-house tools for pulling the entire source.

```sh
$ sudo mkdir -p /usr/local/repo
$ sudo chmod 777 /usr/local/repo
$ cd /usr/local/repo
$ git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
$ export PATH=/usr/local/repo/depot_tools:$PATH
$ umask 022

```

#### Prepare environment

let's go ahead and make a `project` folder, it should look like this.

```sh

$ mkdir -p $HOME/project/chromiumos-R74      # This is the directory to hold Chromium OS source code, name it according to the release you are going to build.
$ mkdir -p $HOME/project/overlays            # This is the directory to hold this repository.

```
Now, we're ready to pull the Chromium OS source code. You will need to authenticate yourself [here](https://chromium.googlesource.com/new-password) before continuing.

But first we need to know which release you'd like to build via using `git ls-remote`

```sh

$ git ls-remote https://chromium.googlesource.com/a/chromiumos/manifest.git | grep release

```
Keep in mind you should be looking for ``release-Rxx-XXXXX.B``, since these are the "stable" versions.

When you're done selecting what repo you need to use, go ahead and run the following commands

```sh

$ cd $HOME/project/chromiumos-R74
$ repo init -u https://chromium.googlesource.com/chromiumos/manifest.git --repo-url https://chromium.googlesource.com/external/repo.git -b release-R80-12739.B
$ repo sync -j8         # Raise this number if you have a fast Internet connection

```

#### Enabling Google Login

If you want to enable Google login in your build, make sure you have `.googleapikeys` in your `$HOME`. `repo` will know what to do with it.

`.googleapikeys` should look like the following:

```yaml
'google_api_key': 'your api key',
'google_default_client_id': 'your client id',
'google_default_client_secret': 'your client secret',
```
Finally, head to this [documentation](http://www.chromium.org/developers/how-tos/api-keys) to get an API key.

#### Prepare the Overlay

Finally, you need to copy this repo to `src/overlays` after cloning inside `project/overlays`.

```sh
$ git clone https://github.com/ClarityCafe/maru.git
$ cp -vRf ./overlay-maru-amd64 cros-source-clone-folder/src/overlays
```

#### Build the image

Once that's all done, all that its left to do is run a this and wait.

```sh
$ export BOARD=maru-amd64
$ cros_sdk -- setup_board --board=${BOARD}
$ cros_sdk -- ./build_packages --withtest --board=${BOARD}
$ cros_sdk -- ./build_image --board=${BOARD} base

```

The build system will make a chroot and build Chromium OS according to the Maru configuration. It should take less than 5 hours to build on a i7 with 8GB RAM, but it could be slower. 

Once the build finishes, you can find your images at `/project/chromiumos-R74/src/build/images/maru-amd64`.

## Installing the image

You can use ``dd`` in Linux or Rufus in DD mode on Windows to write the image to your USB.

Once the image finishes writing to a USB, boot it to your PC, and cross your fingers.

## Contributing

Please read the [Chromium OS Developer Guide](http://www.chromium.org/chromium-os/developer-guide) to get yourself familiarized with how everything works.


### Credits

- arnoldthebat for some of the basis of this work.
- the FlintOS team for the auto-expand-partition ebuild.
- The rest of the Chromium OS team who has been really helpful with this, thank you! <3