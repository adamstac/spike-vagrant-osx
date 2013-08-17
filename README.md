# spike-vagrant-osx

An experiment with getting an OSX guest running with Vagrant so this might eventually be used to test [Sprout](https://github.com/pivotal-sprout/sprout).

## Creating an OSX box for Vagrant with Packer

1. Dependencies

    1. [Vmware Fusion](http://www.vmware.com/products/fusion/overview.html)
    1. [Packer](http://www.packer.io/)
    1. [Vagrant](http://www.vagrantup.com/)
    1. Vagrant's [Vmware Fusion Provider](http://www.vagrantup.com/vmware)
    1. Tim Sutton's [osx-vm-templates](https://github.com/timsutton/osx-vm-templates) for building an OSX box with Packer

1. Clone Tim Sutton's `osx-vm-templates`

    ```
    git clone https://github.com/timsutton/osx-vm-templates
    ```

1. Extract a Packer compatible image from the [OS X Mountain Lion installer](http://osxdaily.com/2012/07/26/redownload-os-x-mountain-lion-mac-app-store/)

    **Caveat:** The installer can't to be downloadable on newer Macbook Airs running OS X 10.8.4 (12E3067)

    ```
    cd osx-vm-templates
    prepare_iso/prepare_iso.sh "/Applications/Install OS X Mountain Lion.app" out
    ```

    Take note of the checksum of the generated image and its full path from the output of this command, you'll need this in a moment.

1. Edit the Packer template for the `packer/template.json`

    1. Edit the checksum so it matches the image generated earlier
    1. Edit the absolute path to the image generated earlier, keeping the `file:///` prefix in the example
    1. Remove the `chef-omnibus.sh` and `puppet.sh` scripts from the list `provisioners`
    1. Don't forget to save the file!

1. Create a new box with Packer

    ```
    cd packer
    packer template.json
    ```

1. Add the box generated earlier to Vagrant

    ```
    vagrant box add osx packer_vmware_vmware.box
    ```

## Starting the OSX guest with Vagrant

1. Clone this repo

    ```
    git clone https://github.com/hiremaga/spike-vagrant-osx
    ```

1. Start vagrant using this repo's Vagrantfile

    ```
    cd spike-vagrant-osx
    vagrant up --provider vmware_fusion
    ```

    **Caveat:** Several Vagrant commands will not work fully because Vagrant is [missing an OSX guest implementation](https://groups.google.com/d/msg/vagrant-up/VyZBqzbVzvs/j0gMiD8ofPUJ), this one of the big the features planned for Vagrant 2.0. So don't worry when you see the error below:

    ```
    The guest operating system of the machine could not be detected!
    Vagrant requires this knowledge to perform specific tasks such
    as mounting shared folders and configuring networks. Please add
    the ability to detect this guest operating system to Vagrant
    by creating a plugin or reporting a bug.
    ```

1. Smoke test the new box

    ```
    vagrant ssh -c 'uname' # → Darwin
    ```
