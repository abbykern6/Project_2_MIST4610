# MIST4610-Project2-Group1
## Team Members:
1. Abigail Kern ([@abbykern6](https://github.com/abbykern6))
2. Lauryn Thomas ([@laurynthomas](https://github.com/laurynthomas))
3. Avery Carabajal ([@avery-carabajal](https://github.com/avery-carabajal))
4. Will Hooks ([@williamhooks912-byte](https://github.com/williamhooks912-byte)) 
## Dataset Description:
This dataset contains information about popular Twitch streamers and their performance metrics on the platform. Twitch is a live-streaming service primarily focused on gaming, entertainment, and creative content. The dataset captures quantitative data that reflects each streamer’s audience engagement and activity levels. It can be used to explore patterns in streaming popularity, community size, and viewer behavior across different streamers.
Dimensions: 
Number of rows: 100 (each representing one streamer)
Number of columns: 6
Each row corresponds to a single streamer, and each column represents a quantitative or categorical variable describing their activity or audience.
## Questions Generated:
### Who is the most successful streamer, and what factors contribute most to their success?
This question seeks to identify which streamer demonstrates the highest level of success based on measurable performance indicators such as total watch time, average viewers, stream time, and followers. By analyzing these quantitative variables, we can develop a multi-dimensional view of what “success” looks like on Twitch — whether it is driven by consistent audience engagement, frequent streaming activity, or a large follower base.
Why it’s important:
Economic impact: Streaming has evolved into a billion-dollar industry, with top streamers generating significant revenue through sponsorships, subscriptions, and advertising. Understanding what drives success can provide valuable insights for emerging creators and marketing professionals aiming to enter this digital economy.
Cultural relevance: Streamers play a key role in shaping online communities and digital entertainment culture. Identifying the factors behind their popularity can help explain modern shifts in media consumption, fandom, and influencer influence.
Data connection: This question ties directly to multiple quantitative fields in the dataset — particularly Total_Watch_Time, Average_Viewers, Stream_Time, and Followers — which together can be used to measure and compare success using both descriptive and inferential analysis.
### What patterns differentiate top-performing streamers from the rest?
This question focuses on identifying broader patterns and trends that distinguish high-performing streamers from those with lower engagement levels. The analysis may include exploring correlations among variables, clustering streamers based on performance metrics, or visualizing patterns across activity and audience size.
Why it’s important:
Social insight: Understanding these patterns highlights how streamers build and sustain audiences — revealing strategies behind engagement, consistency, and community-building.
Economic insight: Companies and content creators can use these findings to optimize marketing efforts, sponsorship targeting, and streaming schedules.
Analytical value: This question encourages data-driven exploration, including techniques such as correlation analysis, ranking metrics, or clustering, all of which directly utilize the dataset’s numeric variables (Average_Viewers, Stream_Time, Followers, etc.).
Broader impact: These insights contribute to understanding digital labor patterns and online success factors, relevant in an era where influencer marketing and live content creation have become mainstream careers.
## Manipulations Applied:
Our data is sourced from an online Twitch dataset. We did not apply any manipulations such as averages or transformations unless they were required for specific queries. However, we did conduct additional research and added a new column classifying the primary language of each channel. This allowed us to introduce more variety into the dataset by incorporating a varchar attribute rather than relying solely on integers and boolean values.
## Question 1 Queries:
### 1) Which streamers have the highest follower base?
SELECT Channel, Followers, Followers_gained
FROM streamers
ORDER BY Followers DESC;
### 2) Which streamers have the highest average viewers?
SELECT Channel, Average_viewers
FROM streamers
ORDER BY Average_viewers DESC
LIMIT 5;
### 3) How engaged is each follower? Calculate Engagement Per Follower:
SELECT
    STREAMER,
    TOTAL_WATCH_TIME,
    FOLLOWERS,
    TOTAL_WATCH_TIME / NULLIF(FOLLOWERS, 0) AS WATCH_TIME_PER_FOLLOWER
FROM TWITCH_STREAMERS
ORDER BY WATCH_TIME_PER_FOLLOWER DESC;
### 4) Do partnered streamers generate more watch time per minute streamed than non-partnered streamers?
SELECT
    Partnered,
    COUNT(*) AS Num_Streamers,
    AVG(Watch_time_minutes / NULLIF(Stream_time_minutes, 0)) AS Avg_WatchTime_Per_StreamSecond
FROM Twitch_Streamers
GROUP BY Partnered
ORDER BY Avg_WatchTime_Per_StreamSecond DESC;
### 5) Find the top performers within each language by comparing each streamer to the max views for their language.
WITH stats AS (
    SELECT
        language,
        name,
        avg_views,
        MAX(avg_views) OVER (PARTITION BY language) AS max_views_lang
    FROM streamers)
SELECT
    language,
    COUNT(*) AS top_streamers,
    AVG(avg_views) AS avg_top_views
FROM stats
WHERE avg_views >= 0.90 * max_views_lang   -- top ~10% based on ratio
GROUP BY language
ORDER BY top_streamers DESC;
## Question 2 Queries:
### 1) Do the streamers who stream the most, get the most views? Is high stream time linked to success?
SELECT Channel,
    "Watch time(Minutes)",
    "Stream time(minutes)",
    ("Watch time(Minutes)" * 100.0 / "Stream time(minutes)") AS percent_viewed
FROM
    streams_data
ORDER BY
    percent_viewed DESC;
### 2) Does being a mature streamer increase watch time? This compares mature vs non mature streamers watch time:
SELECT Mature,
       AVG("Watch time (minutes)") AS avg_watch_time,
       SUM("Watch time (minutes)") AS total_watch_time,
       COUNT(*) AS num_streams
FROM twitch_data
GROUP BY Mature;
### 3) How many followers do the streamers with the 10 lowest average view time have?
SELECT Channel, "Followers", "Average viewers"
FROM twitch_data
ORDER BY "Followers" ASC
LIMIT 10;
### 4) Which channel had the lowest peak viewers , indicating a lack of viral moments?
SELECT 
    Channel, 
    "Peak viewers"
FROM twitch_data
WHERE "Peak viewers" = (
    SELECT MIN("Peak viewers")
    FROM twitch_data
);
### 5) Does being a partnered streamer lead to higher watch-time efficiency?
SELECT
    Partnered,
    COUNT(*) AS num_streamers,
    AVG("Watch time (minutes)" / NULLIF("Stream time(minutes)", 0)) 
        AS avg_watch_time_per_stream_minute
FROM twitch_data
GROUP BY Partnered
ORDER BY avg_watch_time_per_stream_minute DESC;
## Snowflake Dashboard:
https://app.snowflake.com/us-east-1/qoc09333/#/twitchdash-dOKchud1
## Snowflake Graphs
<img width="953" height="331" alt="Screenshot 2025-11-17 at 11 06 16 PM" src="https://github.com/user-attachments/assets/cba409e9-5578-4641-87bf-70cf810e0066" />






