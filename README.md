# GitHub Actions for Preact

This Action for [preact-cli](https://github.com/preactjs/preact-cli) enables arbitrary actions with the `preact` command-line client.

## Inputs

* `args` - **Required**. This is the arguments you want to use for the `firebase` cli


## Environment variables
none

## Example

To authenticate with Firebase, and deploy to Firebase Hosting:

```yaml
name: Build and Deploy
on:
  push:
    branches:
      - master

jobs:
  build:
    name: Build
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repo
        uses: actions/checkout@master
      - name: Install Dependencies
        run: npm install
      - name: Build
        run: npm run build-prod
      - name: Archive Production Artifact
        uses: actions/upload-artifact@master
        with:
          name: dist
          path: dist
  deploy:
    name: Deploy
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repo
        uses: actions/checkout@master
      - name: Download Artifact
        uses: actions/download-artifact@master
        with:
          name: dist
          path: dist
      - name: Deploy to Firebase
        uses: w9jds/firebase-action@master
        with:
          args: deploy --only hosting
        env:
          FIREBASE_TOKEN: ${{ secrets.FIREBASE_TOKEN }}
```
Alternatively:

```yaml
        env:
          GCP_SA_KEY: ${{ secrets.GCP_SA_KEY }}
```


If you have multiple hosting environments you can specify which one in the args line. 
e.g. `args: deploy --only hosting:[environment name]`

## License

The Dockerfile and associated scripts and documentation in this project are released under the [MIT License](LICENSE).


### Recommendation

If you decide to do seperate jobs for build and deployment (which is probably advisable), then make sure to clone your repo as the Firebase-cli requires the firebase repo to deploy (specifically the `firebase.json`)
