# Creating a Video Game Recommender System from Scratch

The dataset for this project can be found [here](https://www.kaggle.com/tamber/steam-video-games/version/1/notebooks).

The goal of this project is to make appropriate recommendations to gamers.

This is accomplished in 3 different ways: User-based Collaborative Filtering, Item-based Collaborative Filtering, and Matrix Factorization.

Of course, the data must be preprocessed first. The original data is structured such that a one user-game pair can have 2 rows: one row that says he/she purchased the game and one row that says how many hours he/she played the game. Here, I am really only interesting in hours played so we can remove the purchase data. There were a lot of extreme outliers (think a couple users playing a few games for a very long time). I chose to log normalize the data so that the distribution looked mostly normal.

I was then able to begin a User-based Collaborative Filtering approach. Now, collaborative filtering is usually done with ratings, where users rate movies/games/songs/shows/etc out of 5 or maybe 10. However, I do not have access to rating data, just hours played. So, I will use hours played as the metric. While this may bring some challenges, it should be able to serve in a pretty similar manner as ratings. One key assumption is being made here: the more a user plays a game, the more they like the game. This may not be completely accurate every time, but it should be good enough.

For user-based filtering, I must first construct a user-user correlation matrix. For two users to have a correlation coefficient, they must have at least *n* games in common, where *n* > 2. This value can be adjusted in the code to test for the best recommendations. A rough description of the collaborative filtering algorithm is:

```
Given a user U:
find U’s average rating Uavg
for game in games not played
	get users who have played it
	get subset of those users who have a similarity rating w/ U
	get top n users by magnitude of correlation
	calculate sum of weighted difference 𝝨w
	add 𝝨w to Uavg = predicted rating
recommend game with highest predicted rating
```

Next, I look at item-based filtering. This process is largely similar to user-based, except I must construct an item-item (game-game) correlation matrix for the algorithm.

Testing both these collaborative filtering algorithms, I found that they performed very well. While I can't 100% verify that the recommendation made for a user is perfect, the games recommended do seem to be similar to the games a user has already played. This is usually a characteristic of a good recommendation engine (most recommendation engines don't really try to get the user to branch out). However, one area is struggled was with users who hadn't played enough games to be correlated with enough users. The algorithm was unable to make predictions for these users. This is known as the **cold-start problem**.

One approach to solving this is Matrix Factorization. This approach attempts to find latent factors that describe all games and users in the dataset. This makes recommendations for users who have only played a couple games possible. It also makes the algorithm for making recommendations trivial. Simply multiply the latent factors for the users and the latent factors for the items together to the user-item matrix. Then, for the user in question, find the highest valued game that the user has not played yet. I chose to use alternating least squares (ALS) with regularization to optimize my matrix factorization. 

The recommendations made by matrix factorization seemed to really favor popular games. For many users, it was recommending DOTA 2, the most popular game in the dataset by far. This is another common problem faced by recommendation engines. So, while it did solve the cold start problem of collaborative filtering, it seemed to introduce a new popularity problem.

The full code for this project can be found [here](recommenders.ipynb).
