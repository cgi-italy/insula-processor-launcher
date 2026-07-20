# insula-processor-launcher

This repository is the entry point for building and deploying Earth Observation
processors on the Insula platform. It exists so that anyone can package a
processor as a container image and publish it to Insula without needing access to,
or knowledge of, the internal build machinery.

## Why this repo exists

Your processor lives in your own public GitHub repository, under your own name. To
turn it into a deployed Insula process it has to be cloned, built into a container
image, scanned, published to the platform registry, and registered with Insula.
This repository holds the single automated workflow that performs those steps.

Keeping that workflow here, separate from the internal pipeline logic, means:

- you are granted access only to this one repository, only to *start* the build;
- the internal build, scan, and publish logic stays in a separate private
  repository that you never need to touch;
- everyone uses the same reviewed, protected workflow, so builds are consistent
  and reproducible.

## What it does

When a build is started for a processor repository, the workflow:

1. clones the public processor repository at the requested branch, tag, or commit;
2. scans the code for committed secrets;
3. builds the container image from `code/Dockerfile`;
4. scans the image for HIGH/CRITICAL vulnerabilities;
5. publishes the scanned image to the platform registry;
6. produces the processor's CWL application package with the published image
   reference filled in.

The final deployment of that CWL to Insula is performed from your own machine by
the command-line tool, so your platform token never enters this system.

## How to use it

You do not edit or push to this repository. You interact with it through the
`insula-processor` command-line tool:

```
insula-processor create --repo-url https://github.com/<you>/<processor>
```

To get started:

- scaffold a processor repository from the template
  `cgi-italy-insula-processors/insula-processor-template`;
- install and authenticate the `insula-processor` CLI;
- run the command above, and re-run it whenever you push changes.

The default branch of this repository is protected: it defines exactly what runs
for every build, and it is not meant to be modified by users.
