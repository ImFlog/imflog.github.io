+++
title="Managing tooling version with ASDF"
date=2023-12-09

[taxonomies]
categories = ["Tips", "Tooling"]
tags = ["ASDF"]

[extra]
comments = true
+++

# The local tooling problem
I always had issues with managing the versions of the tools I use on my computer.
Working on multiple projects, open and private sources implies that some tools are not all stuck on the same version.
So I often end up needing to support multiple versions of the same tool on my computer.
For example, I usually need to support multiple versions of languages : Go, Python, Java, NodeJS to name a few,
but also tools like Terraform, Ansible, MySQL, Postgres ...

So I started to use different tools to handler this:
* [GVM](https://github.com/moovweb/gvm) for Go
* [Pyenv](https://github.com/pyenv/pyenv) for Python
* [SDKMAN](https://sdkman.io/) for Java
* [NVM](https://github.com/nvm-sh/nvm) for NodeJS
* [Tfenv](https://github.com/tfutils/tfenv) for Terraform
* ...

And everything worked fine, but everytime I had to use a new language or tool (recently done some Rust for instance), 
I had to find yet another tool. And I had to remember to update them from time to time.

# Introducing ASDF
That when I found [ASDF](https://asdf-vm.com/): "The Multiple Runtime Version Manager".
As the name implies it's a tool to allow you to manage multiple versions of your runtimes with one tool.

So I gave it a go, and I was not disappointed. After installing it using `brew install asdf`, 
I was able to install the plugins that I needed, they are all listed in [specific GitHub repository](https://github.com/asdf-vm/asdf-plugins).
You can run a single command to install a plugin and then you can install specific runtime version pretty easily:
```bash
asdf plugin add golang
asdf golang install 1.21.1
```

And boom I have a new version of Go installed on my computer.

## Using ASDF in your projects
After you have access to a new version of a runtime, you need to be able to use it in your projects.
With tools like nvm, you could define a specific `.nvmrc` file in your project to specify the version of NodeJS to use.
ASDF has a similar feature, you can create a `.tool-versions` file in your project and specify the version of the runtime to use:
`asdf local golang 1.21.1` for instance will be responsible to populate the `.tool-versions` file with the correct version of Go.
No more need to create multiple file named differently for each tool as everything is centralized in one file.

## Easy migration
Adopting a new tool can be challenging in a team, but ASDF makes it easy.
Indeed, most of the plugins support the non asdf version of the version definition.
For instance for Go, you can use the `go.mod` file to specify the version of Go to use and ASDF will use it to install the correct version.
`.nvmrc` is also supported for NodeJS for instance and so on.

That means that you can start using ASDF in your project without forcing your team to install it 🤩

## Conclusion
ASDF is now one of the first thing I install on a new computer, it's so easy to switch tool version that I can't live without it anymore.
I encourage you to give it a go, and if you have any question, feel free to ask them in the comments.

Flo.