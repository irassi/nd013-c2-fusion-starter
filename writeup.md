# Writeup: Track 3D-Objects Over Time

Please use this starter template to answer the following questions:

### 1. Write a short recap of the four tracking steps and what you implemented there (filter, track management, association, camera fusion). Which results did you achieve? Which part of the project was most difficult for you to complete, and why?

## Step 1
In this step I implemented an *Extended Kalman Filter (EKF)* in *filter.py*. First I implemented the functions to calculate the *system matrix F* and *process noise covariance Q.*

These were then used in the calculations of the method *"predict()"* that was used to predict *state x* and *estimation error covariance P* to next timestep and then save *x* and *P* in a track.

Then in method *"update()"* I update *state x* and *covariance P* with an associated measurement and then save the *x* and *P* in a track. Methods for counting the necessary *gamma* and *S* variables is also included.

At this point in the project only one target is tracked. Resulting RMSE plot shown below.

![Step 1 RMSE Plot](img/Step1-RMSE_plot.png)

## Step 2
In Step 2 I implemented track management to initialize and delete tracks, set a track state and a track score in *trackmanagement.py*. I created an initialization for the track values based on measurement data. The initialized track was assigned an *'initialized'* state and a track score was created. 

Method *"manage_tracks()"* is used to handle the decreasing of track score for unassigned tracks and deleting tracks based on the following criteria.

    track.state == 'confirmed' and track.score <= params.delete_threshold) \
    or track.state != 'confirmed' and (track.P[0, 0] > params.max_P \
    or track.state != 'confirmed' and track.P[1, 1] > params.max_P) \
    or (track.score < 0.05

Then the method *"handle_updated_track()"* is used to increase the track score for a given track and an set its state to *'confirmed'* if it exceeds the confirmation threshold set in the *params* or *'tentative'* otherwise.

Only one target is tracked in this step as well, resulting RMSE Plot shown below.
![Step 2 RMSE Plot](img/Step2-RMSE_plot.png)
## Step 3
Step 3 introduced multiple target tracking to the project, by using single nearest neighbor data association to associate measurements to tracks in *association.py*. *Mahalanobis distance (MHD)* formula is used to calculate the distances between all targets and measurements. 

I initialized an *association matrix* with infinite values and update it with the MHD values only if it meets the *gating* criteria tested with the *'gating()'* method.

The RMSE Plot showing multiple tracked targets shown below. The mean values for all tracks are below 0.2.

![Step 3 RMSE Plot](img/Step4-RMSE_plot.png)

## Step 4
In the final step I introduced camera-lidar fusion by implementing a nonlinear camera measurement model and checking for camera visibility in *measurements.py*. 

The method *'in_fov()'* checks if the object is in the field of view for the sensor. This information is used to decide if the track score should be decreased in *trackmanagement.py*.

Method *'get_hx()'* is updated with the nonlinear camera measurement function that is used for camera sensors. 

The results of this step are shown in the RMSE Plot below and the movie file "results\my_tracking_results.avi" which unfortunately is too big to include in GitHub. I added the movie file to my Google Drive and you should be able to access it from this folder https://drive.google.com/drive/folders/1PdGI9VZm2wHuwZ5VpsRn3a8W_M8K1-i2?usp=share_link

The plot shows that track 0 and track 1 are followed from beginning to end without interruptions and their RMSE mean values are at a great level. 

![Step 4 RMSE Plot](img/Step4-RMSE_plot.png)

### 2. Do you see any benefits in camera-lidar fusion compared to lidar-only tracking (in theory and in your concrete results)? 
Cameras can provide information that a lidar can't, such as color or texture of an object. This information can be of assistance in distinguishing between two similar or overlapping objects. Also in general the images can provide important contextual information such as road signs. 

In my project implementation and using the camera visibility checks I was able to achieve a low RMSE value which speaks for itself.

### 3. Which challenges will a sensor fusion system face in real-life scenarios? Did you see any of these challenges in the project?

Some challenges of sensor fusion systems I believe could be sensor occlusion, calibration, and environment. By sensor occlusion I mean that in a real-world scenario the objects that are tracked are not 100% visible at all times and can be blocked by other objects. The tracking needs to be able to handle this.

Calibration is crucial for any sensor, and since a self-driving car will in most cases have quite many sensors that need to work together, the importance is even greater.

Environmental factors like rain or snow can decrease lidar performance, where as cameras need adequate light and visibility to function. These factors need to be considered and mitigated in the sensor fusion system.


### 4. Can you think of ways to improve your tracking results in the future?

The results in this project were achieved with a simple nearest neighbor calculation, more advanced data association could be implemented for even greater results. 

Also the default parameters could be adjusted in an attempt to improve the results, but the RMSE values are already at a good level in my opinion.
