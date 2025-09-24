# Dataset Description

This document summarizes the processed Weibo datasets used by the ITS-WeakSignal pipeline.  
Each CSV file represents posts for one city and follows a common schema.  


---

## Files and Sizes

| City      | File Name                | Rows (posts) | Columns | Unique Users | Time Coverage                 |
|-----------|--------------------------|--------------|---------|--------------|-------------------------------|
| Beijing   | `beijing_processed.csv`  | **805**      | 17      | **664**      | 2019-09-07 → 2019-11-19       |
| Shanghai  | `shanghai_processed.csv` | **588**      | 17      | **518**      | 2017-01-16 → 2019-11-16       |
| Xiamen    | `xiamen_processed.csv`   | **671**      | 17      | **444**      | 2016-08-13 → 2019-11-02       |
| **Total** | —                        | **2,064**    | 17      | —            | —                             |


> - **Beijing**: 805 posts, 664 unique users, 2019-09-07 → 2019-11-19  
> - **Shanghai**: 588 posts, 518 unique users, 2017-01-16 → 2019-11-16  
> - **Xiamen**: 671 posts, 444 unique users, 2016-08-13 → 2019-11-02  

These numbers are computed from the CSVs’ `user_id` and `timestamp` columns (parsing timestamps with `errors="coerce"` and counting non-null uniques).

---

## Schema (column dictionary)

All three city files share the same 17 columns:

| Column          | Type     | Description                                a                                 |
|-----------------|----------|-----------------------------------------------------------------------------|
| `seq_id`        | int/str  | Monotonic sequence or internal row id                                       |
| `screen_name`   | str      | User display name                                                           |
| `user_id`       | str/int  | **User identifier** (stable across posts)                                   |
| `user_bio`      | str      | Free-text bio or profile description                                        |
| `verification`  | int/bool | Account verification flag/category                                          |
| `timestamp`     | str/dt   | **Post creation time** (ISO-like string)                                    |
| `influence`     | float    | Precomputed post-level influence \(Q_i(t)\) or related proxy (0–1 scale)    |
| `followers`     | int      | Follower count at crawl time                                                |
| `following`     | int      | Following count                                                             |
| `likes`         | int      | Like count for the post                                                     |
| `comments_count`| int      | Total number of comments                                                    |
| `text`          | str      | Post content (tokenized/cleaned if applicable)                              |
| `comments_list` | str/json | (Optional) serialized comment ids/snippets                                  |
| `comments_24h`  | int      | Comments within 24h (used in i-IDF / temporal popularity)                   |
| `comment_times` | str/json | Serialized inter-arrival times or timestamps of comments                    |
| `timestamp_raw` | str      | Original timestamp string (pre-parsing)                                     |
| `city`          | str      | City label (`Beijing`, `Shanghai`, `Xiamen`)                                |

---

## Keys Used by the Pipeline

- **ID**: `user_id` (for unique users)  
- **Time**: `timestamp` (for windowing; parsed to datetime)  
- **Engagement**: `likes`, `comments_count`, `comments_24h`, `comment_times`  
- **Influence**: `influence` (or computed from engagement + followers as in paper §User Weight Construction)



