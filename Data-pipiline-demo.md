# Demo Project Description

We've developed a React-based web application that includes a map with a map-click handler allowing users to create points with latitude and longitude data. 
My goal is to enable users to save these points as GeoJSON data in Firebase storage when they click the send button on the web app. 
To achieve this, I plan to run an R script using Firebase's Cloud Run and Docker capabilities. 
After running the script, I intend to save the results back to Firebase storage. Finally, I want to display the resulting data on the web app as text.

<img src="https://user-images.githubusercontent.com/70645899/231495933-803aa787-3fe8-4a8f-b8de-d141b9db107e.jpg" width="500">


# Higher Overview

1. Set up Firebase in your React web app.
2. Save the lat, lng data as a GeoJSON object and upload it to Firebase Storage.
3. Create a Docker container with R, R script, and necessary dependencies.
4. Deploy the Docker container to Google Cloud Run.
5. Trigger the R script in the Cloud Run container after uploading the GeoJSON file to Firebase Storage.
6. Save the R script output back to Firebase Storage.
7. Retrieve and display the R script output in your React web app.


# Step by Step

## 1) Set up Firebase in your React web app

```javascript

import firebase from 'firebase/app';
import 'firebase/storage';

const firebaseConfig = {
  // Your Firebase configuration
};

firebase.initializeApp(firebaseConfig);
const storage = firebase.storage();

```

## 2) Save the lat, lng data as a GeoJSON object and upload it to Firebase Storage

```javascript

const sendLatLngData = async (lat, lng) => {
  const geojson = {
    type: 'FeatureCollection',
    features: [
      {
        type: 'Feature',
        geometry: {
          type: 'Point',
          coordinates: [lng, lat],
        },
      },
    ],
  };

  const file = new Blob([JSON.stringify(geojson)], { type: 'application/json' });
  const uploadTask = storage.ref('geojson/' + Date.now() + '.json').put(file);

  // Upload GeoJSON file to Firebase Storage
  await uploadTask;
};

```

## 3) Create a Dockerfile for your R script

```dockerfile

FROM r-base

RUN apt-get update && apt-get install -y \
    libcurl4-openssl-dev \
    libssl-dev \
    libxml2-dev

RUN R -e "install.packages(c('your_required_packages'), repos='http://cran.rstudio.com/')"

COPY your_r_script.R /your_r_script.R

CMD ["Rscript", "your_r_script.R"]

```

## 4) Deploy the Docker container to Google Cloud Run

```
Follow the official guide to build and deploy the container.
https://cloud.google.com/run/docs/quickstarts
```

## 5) Trigger the R script in the Cloud Run container

```
To trigger the R script in the Cloud Run container after uploading the GeoJSON file to Firebase Storage, you can use Firebase Cloud Functions to listen for changes in Firebase Storage and make an HTTP request to your Cloud Run service.
Follow the official guide to set up Cloud Functions for Firebase, and then create a function like this
```

```javascript

const functions = require('firebase-functions');
const admin = require('firebase-admin');
const fetch = require('node-fetch');
admin.initializeApp();

exports.runRScript = functions.storage.object().onFinalize(async (object) => {
  const url = `https://${process.env.GOOGLE_CLOUD_RUN_URL}/invoke_r_script`; // Replace with your Cloud Run service URL

  const response = await fetch(url, {
    method: 'POST',
    body: JSON.stringify({ object }),
  });

  const result = await response.text();
  console.log('R script result:', result);
});


```

## 6. Save the R script output back to Firebase Storage

```

Modify your R script to save the output to Firebase Storage using the googleCloudStorageR package

```

## 7. Retrieve and display the R script output in your React web app


```javascript

import { useState, useEffect } from 'react';

const App = () => {
  const [output, setOutput] = useState('');

  const getRScriptOutput = async () => {
    // Replace 'output/output_file.txt' with the actual path to your R script output in Firebase Storage
    const url = await storage.ref('output/output_file.txt').getDownloadURL();

    const response = await fetch(url);
    const text = await response.text();
    setOutput(text);
  };

  useEffect(() => {
    getRScriptOutput();
  }, []);

  return (
    <div className="App">
      <h1>R Script Output</h1>
      <pre>{output}</pre>
    </div>
  );
};

export default App;



```
