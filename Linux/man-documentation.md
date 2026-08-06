---
title: The Linux man Command Has More to Offer Than You Think
tags:
- Linux
- Debian
- Ubuntu
- DevOps
- System Administration
published: '2026-08-02'
free: false
freedium_url: https://freedium-mirror.cfd/https://medium.com/@u.mair/the-linux-man-command-has-more-to-offer-than-you-think-da5b34affc39
source_url: https://medium.com/@u.mair/the-linux-man-command-has-more-to-offer-than-you-think-da5b34affc39
---

# The Linux man Command Has More to Offer Than You Think

*Published Aug 02, 2026 · Free: No*

At first glance, the `man`command looks pretty dull. You type a command, a wall of text appears, you scroll for thirty seconds, then quit without reading half of it.

Most Linux users never get past that stage. The funny thing is that `man` is one of the most organized documentation systems you'll ever use. Once you understand how it's structured, finding the information you need becomes much easier.

### Every manual page belongs to a section

Manual pages are divided into numbered sections. Most Linux systems include sections **1** through **9**, and some distros also ship additional sections such as **n**, **l**, or **p** for software-specific documentation.

The search paths and section order are configured in:

```bash
/etc/manpath.config
```

You can certainly open it if you are curious, although unless you've worked with the man page system before, it mostly looks like configuration noise.

A better place to start is with the introduction pages. Each manual section has an **intro** page that explains what belongs in that section.

For example, on Debian or Ubuntu, install the development man pages if you do not already have them:

```typescript
sudo apt install manpages-dev
```

Then open the introduction to section 1:

```typescript
man 1 intro
```

You'll see an overview covering topics such as:

- Logging in
- Shells
- Pathnames
- Directories
- Filesystems

along with many other categories covered by the first section.

### Section 1: Everyday commands

Section 1 is where you'll spend most of your time. It documents standard user commands, utilities, and programs. The problem is that there are hundreds of them.

Fortunately, you don't have to remember every command name. If you are looking for something related to copying files, for example, search the manual database instead:

```go
man -k copy
```

The output might look like this:

```sql
cp (1)        - copy files and directories
cpio (1)      - copy files to and from archives
dd (1)        - convert and copy a file
cppw (8)      - copy with locking the given file
```

The number in parentheses tells you which manual section contains the page.

If you want information about the administrative utility `cppw`, simply run:

```typescript
man 8 cppw
```

This is often much faster than searching the web.

### Section 2: System calls

Section 2 documents Linux system calls. These are the interfaces programs use to ask the kernel to perform operations such as creating files, allocating memory, or launching processes.

For example:

```bash
man 2 mkdir
```

This is **not** documentation for the `mkdir` command you run in your shell. Instead, it describes the `mkdir()` system call that applications use internally. If you've ever wondered how Linux programs interact with the kernel, this section is worth exploring.

### Section 3: Library functions

Section 3 is aimed primarily at programmers. Instead of documenting kernel interfaces, it covers functions provided by libraries such as the C standard library.

If you are writing software in C or debugging existing code, you'll probably spend a lot of time here.

### Section 4: Device files

This section documents special files found under:

```bash
/dev
```

These represent hardware devices and various kernel interfaces exposed through the filesystem.

### Section 5: File formats and configuration files

One of the most underrated sections. It documents configuration files, their syntax, and the meaning of various options.

For example:

```typescript
man 5 login.defs
```

This page explains configuration options used by user management tools.

Among other things, you'll find settings such as `SYS_UID_MIN` and `SYS_UID_MAX`, which define the range of user IDs automatically assigned when creating new accounts with tools like `useradd`.

Whenever you are editing a configuration file and wondering what a particular option does, check whether it has a section 5 man page before opening your browser.

### Section 6: Games

Yes, there's an entire section dedicated to games. Historically, Unix systems shipped with a surprising number of small terminal games, and this section documents them.

Modern desktop distros rarely install many of them by default, but the section still exists.

### Section 7: Standards and conventions

Section 7 explains concepts rather than commands. You'll find documentation covering boot procedures, protocols, naming conventions, file patterns, and other system behavior.

Some useful examples include:

```typescript
man 7 boot
```

for the Linux boot process,

```typescript
man 7 bootparam
```

for kernel boot parameters, and

```perl
man 7 glob
```

for shell wildcard pattern matching.

These pages answer the "how does Linux work?" questions that ordinary command documentation usually doesn't cover.

### Section 8: System administration

Section 8 contains commands intended primarily for system administrators.

Examples include:

```typescript
man 8 mount
```

for mounting filesystems,

```typescript
man 8 netstat
```

for legacy network statistics utilities, and

```typescript
man 8 hdparm
```

for configuring and querying storage devices. If a command requires root privileges or performs system administration tasks, there's a good chance you'll find it here.

### Section 9: Kernel interfaces

Section 9 is the least common. Some Unix variants use it extensively, but many Linux distros either leave it empty or omit it entirely.

If you are looking for kernel-specific documentation and aren't sure where it lives, `man -k` is usually the quickest way to find it.

### Did you know `man` can open in your browser?

If you would rather read documentation with hyperlinks and proper formatting, `man` can generate HTML pages. On Debian or Ubuntu, install the required packages:

```typescript
sudo apt install man-db groff
```

Then run:

```typescript
man -H bash
```

The manual page is converted to HTML and opened in your default web browser. If you are working on a machine without a graphical environment, the HTML file is still generated. It is usually placed somewhere under `/tmp`, where you can open it later or copy it to another system.