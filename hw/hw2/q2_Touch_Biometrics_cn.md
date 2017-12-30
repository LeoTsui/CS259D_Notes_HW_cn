# Touch Biometrics

## Questions

### a

Implement two more features in addition to the 30 found in the database. Do they have positive information gain? That is, are the features useful?

### b

Report correlation of these feature to the rest of the implemented features.

### c

Train your model on a binary classifier of your choice (“true user” or “false user” classification problem) using the following 4 scenarios in which you use a feature selection method to choose top 10 features. Describe this process. Use 10-fold cross validation to compute precision and recall in the following scenarios. Try to maximize F1 score when optimizing your classifier. Report F1 and any methods you used to optimize your classifier.

* i. 10 top features,
* ii. 10 top features & your features
* iii. 30 computed features,
* iv. 30 computed features & your features

### d

Qualitatively describe which family of features are most discriminating in your classifier.

## Data

1. phone I
1. user ID
1. document ID
1. time[ms]
1. action
1. phone orientation
1. x-coordinate
1. y-coordinate
1. pressure
1. area covered
1. finger orientation

## Feature

* feature csv
    1. ~~user_id~~
    1. ~~doc_id~~
    1. inter_stroke_time
    1. stroke_duration
    1. start_x
    1. start_y
    1. stop_x
    1. stop_y
    1. direct_end_to_end_distance
    1. mean_resultant_lenght
    1. **up_down_left_right_flag**
    1. direction_of_end_to_end_line
    1. ~~phone_id~~
    1. 20_pairwise_velocity
    1. 50_pairwise_velocity
    1. 80_pairwise_velocity
    1. 20_pairwise_acc
    1. 50_pairwise_acc
    1. 80_pairwise_acc
    1. median_velocity_at_last_3_pts
    1. largest_deviation_from_end_to_end_line
    1. 20_dev_from_end_to_end_line
    1. 50_dev_from_end_to_end_line
    1. 80_dev_from_end_to_end_line
    1. average_direction
    1. length_of_trajectory
    1. ratio_end_to_end_dist_and_length_of_trajectory
    1. average_velocity
    1. median_acceleration_at_first_5_points
    1. mid_stroke_pressure
    1. mid_stroke_area_covered
    1. **mid_stroke_finger_orientation**
    1. ~~change_of_finger_orientation~~
    1. **phone_orientation**
* 30 features
    1. `inter_stroke_time`
    1. `stroke_duration`
    1. `start_x`
    1. `start_y`
    1. `stop_x`
    1. `stop_y`
    1. `direct_end_to_end_distance`
    1. `mean_resultant_lenght`
    1. `up_down_left_right_flag`
    1. `direction_of_end_to_end_line`
    1. `20_pairwise_velocity`
    1. `50_pairwise_velocity`
    1. `80_pairwise_velocity`
    1. `20_pairwise_acc`
    1. `50_pairwise_acc`
    1. `80_pairwise_acc`
    1. `median_velocity_at_last_3_pts`
    1. `largest_deviation_from_end_to_end_line`
    1. `20_dev_from_end_to_end_line`
    1. `50_dev_from_end_to_end_line`
    1. `80_dev_from_end_to_end_line`
    1. `average_direction`
    1. `length_of_trajectory`
    1. `ratio_end_to_end_dist_and_length_of_trajectory`
    1. `average_velocity`
    1. `median_acceleration_at_first_5_points`
    1. `mid_stroke_pressure`
    1. `mid_stroke_area_covered`
    1. `mid_stroke_finger_orientation`
    1. `phone_orientation`
* 2 new features
    * 20\%-perc. pressure
    * 80\%-perc. area covered
* Top 10
    * 22 average_direction
    * 10 direction_of_end_to_end_line
    * 25 average_velocity
    * 13 80_pairwise_velocity
    * 12 50_pairwise_velocity
    * 2 stroke_duration
    * 15 50_pairwise_acc
    * 8 mean_resultant_lenght
    * 14 20_pairwise_acc
    * 18 largest_deviation_from_end_to_end_line
