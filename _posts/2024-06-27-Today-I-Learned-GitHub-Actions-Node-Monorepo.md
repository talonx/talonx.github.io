---           
layout: post
title: Today I Learned - How To Setup GitHub Actions for a Node Monorepo 
date: 2024-06-27 00:00:00 IST
comments: true
categories: today-I-learned javascript github-actions
---

A monorepo is a repository with more than one logical project. I was trying to setup GitHub Actions to build my repository
with two distinct projects inside it. GitHub Actions will run your YAML configuration whenever you commit. 
Turns out it's not so straightforward.

This is what my project structure looks like
```
project-root
  - frontend
    - package-lock.json
    - src/
    - ... <etc>
  - backend
    - package-lock.json
    - src/
    - ... <etc>
```

The autogenerated node.js.yml looks like this
```yaml
name: Node.js CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [14.x, 16.x, 18.x]
        # See supported Node.js release schedule at https://nodejs.org/en/about/releases/
    steps:
    - uses: actions/checkout@v4
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
    - run: npm ci
    - run: npm run build --if-present
```

How do I specify that the jobs have to run inside each subdirectory? And there is no top level package-lock.json (which is where
some of the proposed solutions break)

A combination of [SO](https://stackoverflow.com/a/71281459/10996) and [Medium](https://medium.com/@owumifestus/configuring-github-actions-in-a-multi-directory-repository-structure-c4d2b04e6312) 
posts and a [GitHub issue comment](https://github.com/actions/setup-node/issues/706#issuecomment-1557983832) helped me to get to a working configuration. The key points are
- Use the "matrix" keyword to declare the equivalent of "run this for d in directories" where directories is the list of your subdirectories
- Specify a working-directory under defaults, with the value being $d from the matrix
- Specify the cache-dependency-path with the same $d/package-lock.json
- Add an npm install before doing anything

The final working YAML looks like this
```yaml
  # This workflow will do a clean installation of node dependencies, cache/restore them, build the source code and run tests across different versions of node
# For more information see: https://docs.github.com/en/actions/automating-builds-and-tests/building-and-testing-nodejs

name: Node.js CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix: { dir: ['./backend', './frontend'], node-version: ['20.x'] }
        # See supported Node.js release schedule at https://nodejs.org/en/about/releases/
    defaults:
      run:
        working-directory: ${{ matrix.dir }}
    steps:
    - uses: actions/checkout@v4
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
        cache-dependency-path: '${{ matrix.dir }}/package-lock.json'
    - run: npm install
    - run: npm ci
    - run: npm run build --if-present
#    - run: npm run test
```
Note that I did not have to include the node-version in the matrix - it is just to illustrate the syntax. 

Thank you for reading, and do reach out via comments or on [Twitter](https://twitter.com/talonx){:target="_blank"} if you want to chat or share your thoughts.