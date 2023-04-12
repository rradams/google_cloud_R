# Demo Project Description

We've developed a React-based web application that includes a map with a map-click handler allowing users to create points with latitude and longitude data. 
My goal is to enable users to save these points as GeoJSON data in Firebase storage when they click the send button on the web app. 
To achieve this, I plan to run an R script using Firebase's Cloud Run and Docker capabilities. 
After running the script, I intend to save the results back to Firebase storage. Finally, I want to display the resulting data on the web app as text.




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
