# Music Recommendation Algorithm — Project Report

**Dataset:** Music Dataset: Lyrics and Metadata from 1950 to 2019
**Algorithm:** KMeans Clustering (Unsupervised Learning)


## Question 1: What insights did you gain from EDA? Were any columns
highly correlated?

### Dataset Overview

The training dataset contains 28,362 songs spanning from 1950 to 2019
across 7 genres including pop, rock, country, jazz, blues, reggae,
and hip hop. Each song has 17 numeric features consisting of 14 lyrical
theme scores each ranging from 0 to 1, a word count column (len),
an age score, and a sadness score.

### Key EDA Findings

**Genre distribution:**
The dataset is not evenly balanced across genres. Pop makes up the
largest share of songs followed by rock and country. This reflects
the dominance of these genres in recorded music from 1950 to 2019.

**Score distributions:**
All lyric score features are heavily right-skewed. The vast majority
of songs score close to 0 on any given theme with a small number
scoring very high. This makes intuitive sense — most songs do not
focus intensely on a single theme like violence or gospel. Instead
they blend multiple themes at low intensity with one or two dominant
characteristics that define their lyrical identity.

**Genre personality differences:**
The grouped bar charts and box plots confirmed that genres have
distinct lyrical personalities. Rock songs score significantly higher
on violence. Gospel and country songs score higher on family/gospel
and family/spiritual themes. Pop songs show more variation across
romantic and dating scores. Jazz songs tend to score higher on
communication and night/time themes reflecting their storytelling
and late-night cultural associations.

**Age distribution:**
Songs are reasonably distributed across decades with the age score
ranging from 0 (newest, 2019) to 1 (oldest, 1950). The dataset
has good representation across all eras which means our clusters
are not dominated by any single time period.

### Correlation Analysis

The correlation heatmap and pairwise analysis found no feature pairs
with an absolute correlation above the 0.7 threshold. This is a
strong positive finding — it means all 17 features carry genuinely
distinct information and none are redundant duplicates of each other.

The highest correlations observed were mild and below the threshold
meaning no features needed to be dropped for redundancy reasons.
All numeric features were retained for clustering giving KMeans
a rich and non-redundant feature space to work with.

---

## Question 2: How did you determine which columns to drop or keep?

### Columns Dropped — Non-Numeric

The following columns were dropped because KMeans is a mathematical
algorithm that can only operate on numbers:

| Column | Reason for Dropping |
|--------|-------------------|
| artist_name | Text identifier — not a lyrical feature |
| track_name | Text identifier — not a lyrical feature |
| release_date | Already captured numerically by the age column |
| genre | Categorical label — we want the model to discover its own groups |
| topic | Categorical label — same reason as genre |
| lyrics | Raw text — cannot be converted to a number. Also explicitly excluded by the project instructions |

### Columns Dropped — Correlation

No columns were dropped for correlation reasons. The pairwise
correlation analysis found no feature pairs exceeding the absolute
correlation threshold of 0.7. All 17 numeric features were retained.

### How EDA Informed These Decisions

The correlation heatmap produced in Notebook 1 was the direct
evidence used to make the column retention decision. Because no
strong correlations were found, dropping any numeric column would
have removed real unique information rather than eliminating
redundancy. The EDA confirmed that each lyric theme score captures
a genuinely different dimension of lyrical content.

---

## Question 3: What was the optimal number of clusters?

### Method 1 — Elbow Method
*(See docs/12_elbow_plot.png)*

We trained KMeans for k=2 through k=12 and plotted inertia for each
value. Inertia measures the total within-cluster sum of squared
distances — lower values indicate tighter and more compact clusters.
The curve showed a noticeable bend beginning around k=8 where the
rate of improvement began to flatten significantly. Beyond this point
each additional cluster produced diminishing returns in cluster
tightness.

### Method 2 — Silhouette Score
*(See docs/13_silhouette_scores.png)*

The silhouette score measures how well each song fits its own cluster
versus its nearest neighboring cluster. Scores range from -1 to +1
with higher being better.

Our results:

| k | Silhouette Score |
|---|-----------------|
| 2 | 0.0879 |
| 3 | 0.0914 |
| 4 | 0.1136 |
| 5 | 0.1291 |
| 6 | 0.1470 |
| 7 | 0.1516 |
| 8 | 0.1778 |
| 9 | 0.1735 |
| 10 | 0.1830 |
| 11 | 0.1806 |
| 12 | 0.1783 |

The largest single improvement in the entire range occurred between
k=7 (0.1516) and k=8 (0.1778) — a gain of 0.0262 points. After k=8
the scores plateau with k=9 actually dropping before recovering
marginally at k=10.

### Decision — Optimal k = 8

While k=10 produces the highest absolute silhouette score of 0.1830,
the improvement over k=8 is marginal at only 0.0052 points. The most
significant structural change in the data occurs at k=8 which
represents the point of genuine cluster stabilization.

We chose k=8 because it represents the best balance between cluster
quality and cluster size. Larger clusters also produce better and
more varied song recommendations in Notebook 4 since there are more
songs to sample from within each cluster.

---

## Question 4: Describe the clusters in human terms

*(See docs/15_cluster_profiles_heatmap.png and
docs/16_cluster_genre_breakdown.png)*

Our KMeans model with k=8 discovered the following clusters based
entirely on lyrical content — no genre labels were used during
training.

---

### Cluster 0 — Night Life and Time-Oriented Songs
**Dominant themes:** night/time (0.398), communication (0.076),
sadness (0.057)

**Human description:**
These songs are defined by their strong association with nightlife,
time passing, and late-night experiences. The night/time score of
0.398 is by far the highest of any theme in this cluster making it
the clearest and most distinctive cluster in the model. Songs here
tend to evoke the atmosphere of evenings out, waiting for someone,
or reflecting on time. The moderate sadness score adds an emotional
undertone of longing or reflection.

**Test songs assigned here:**
- Dennis Brown — Second Chance (reggae)
- Randy Travis — Messin' With My Mind (country)

---

### Cluster 1 — World and Life Reflection Songs
**Dominant themes:** world/life (0.429), communication (0.079),
sadness (0.062)

**Human description:**
These songs deal with broad observations about the world, life in
general, and the human condition. The world/life score of 0.429
dominates this cluster clearly. Songs here tend to be thoughtful
and observational — reflecting on society, existence, and everyday
experiences rather than focusing on specific emotions like romance
or violence. The communication score suggests these songs often
involve conveying a message or perspective to the listener.

**Test songs assigned here:**
- Godsmack — Immune (rock)

---

### Cluster 2 — Intense and Violent Songs
**Dominant themes:** violence (0.433), communication (0.073),
light/visual perceptions (0.058)

**Human description:**
This is the most lyrically intense cluster in the dataset. The
violence score of 0.433 clearly defines these songs as dealing
with conflict, aggression, tension, and confrontation. Despite
the genre labels of the test songs assigned here being pop and
jazz, the clustering shows that lyrical content cuts across genre
boundaries — a pop song can be lyrically just as intense as a
rock song. The light/visual perceptions score suggests vivid and
dramatic imagery in the lyrics.

**Test songs assigned here:**
- The Black Crowes — Sister Luck (pop)
- Noro Morales — Silencio (jazz)
- Paramore — Playing God (pop)

---

### Cluster 3 — Music About Music Songs
**Dominant themes:** music (0.410), light/visual perceptions (0.063),
communication (0.062)

**Human description:**
This is a fascinating and highly specific cluster — songs whose
lyrics are primarily about music itself. The music score of 0.410
is the defining characteristic making this one of the cleanest
and most interpretable clusters in the model. These are songs that
reference instruments, performance, sound, melody, and the act
of making or listening to music. No test songs were assigned to
this cluster.

---

### Cluster 4 — Sadness and Emotional Songs
**Dominant themes:** sadness (0.436), communication (0.092),
world/life (0.055)

**Human description:**
These are the emotionally heavy songs in the dataset. The sadness
score of 0.436 is the highest single theme score across all clusters
making this the most clearly defined emotional cluster. Songs here
deal with loss, heartbreak, grief, longing, and emotional pain.
The high communication score suggests these songs involve direct
emotional expression — speaking to someone about feelings of hurt
or longing. The slight world/life presence adds a philosophical
dimension to the sadness.

**Test songs assigned here:**
- Jerry Lee Lewis — Your Cheating Heart (pop)
- Taste — Railway and Gun (blues)

---

### Cluster 5 — Romantic Songs
**Dominant themes:** romantic (0.402), communication (0.078),
sadness (0.072)

**Human description:**
These are the love songs of the dataset. The romantic score of
0.402 clearly defines this cluster as containing songs about
love, attraction, desire, and intimate relationships. The moderate
sadness score adds nuance — many romantic songs carry an undercurrent
of longing, vulnerability, or unrequited love. The communication
score reflects the conversational nature of romantic lyrics which
often directly address a loved one.

**Test songs assigned here:**
- Paul Anka — Eso Beso (pop)

---

### Cluster 6 — Obscene and Provocative Songs
**Dominant themes:** obscene (0.460), movement/places (0.069),
communication (0.068)

**Human description:**
This cluster contains the most provocative and raw songs in the
dataset. The obscene score of 0.460 is the highest single theme
score in the entire model making this the most distinctive cluster.
Songs here deal with explicit content, the rockstar lifestyle,
money, and provocative themes. The movement/places score suggests
lyrics that reference specific locations or physical movement
adding an energetic and dynamic quality. Despite being assigned
a rock song from Rage Against the Machine, this cluster captures
lyrical intensity that crosses genre lines.

**Test songs assigned here:**
- Rage Against the Machine — Pistol Grip Pump (rock)

---

### Cluster 7 — General and Mixed Theme Songs
**Dominant themes:** sadness (0.066), communication (0.077),
dating (0.031)

**Human description:**
This is the most general cluster in the model. No single theme
dominates strongly — all scores are relatively low and balanced.
These are songs with mixed or moderate lyrical content that does
not strongly align with any single theme. They represent the broad
middle ground of popular music that blends multiple themes without
focusing intensely on any one. No test songs were assigned here.

---

## Question 5: Which songs would you recommend to this user?

*(See data/recommendations.csv for the full table)*

### How Clusters Were Assigned to Test Songs

| Test Song | Artist | Genre | Cluster | Defining Theme |
|-----------|--------|-------|---------|---------------|
| Immune | Godsmack | Rock | 1 | World/Life |
| Second Chance | Dennis Brown | Reggae | 0 | Night/Time |
| Sister Luck | The Black Crowes | Pop | 2 | Violence |
| Your Cheating Heart | Jerry Lee Lewis | Pop | 4 | Sadness |
| Eso Beso | Paul Anka | Pop | 5 | Romantic |
| Silencio | Noro Morales | Jazz | 2 | Violence |
| Pistol Grip Pump | Rage Against the Machine | Rock | 6 | Obscene |
| Railway and Gun | Taste | Blues | 4 | Sadness |
| Messin With My Mind | Randy Travis | Country | 0 | Night/Time |
| Playing God | Paramore | Pop | 2 | Violence |

### User Listening Profile

The 10 test songs spread across 6 clusters revealing a user with
diverse but identifiable musical tastes:

- **3 songs in Cluster 2** (Violence) — the user gravitates toward
  lyrically intense and confrontational songs regardless of genre
- **2 songs in Cluster 0** (Night/Time) — the user enjoys atmospheric
  songs with a sense of time and place
- **2 songs in Cluster 4** (Sadness) — the user connects with
  emotionally heavy and heartfelt lyrics
- **1 song each** in Clusters 1, 5, and 6 — showing breadth across
  world/life reflection, romance, and provocative content

### Recommendation Logic

For each test song we identified its cluster and sampled 5 songs
from the same cluster in the 28,362-song training set. Songs in
the same cluster share similar lyrical DNA — similar scores across
all 17 lyric theme features. The cluster acts as a lyrical
fingerprint connecting what the user already loves to songs they
have not yet discovered.

### Sample Recommendations

**Because the user listened to Godsmack — Immune (Cluster 1 — World/Life):**

This rock song was assigned to the world/life cluster indicating
its lyrics deal with broad observations about society and existence
rather than pure aggression. We recommend:
1. Dolly Parton — Wildflowers (country)
2. The Cranberries — Dreaming My Dreams (pop)
3. Rebelution — Take On Anything (reggae)
4. Chicago — Will You Still Love Me? (rock)
5. Willie Nelson — Wonderful Future (country)

**Because the user listened to Dennis Brown — Second Chance
(Cluster 0 — Night/Time):**

This reggae song was placed in the night/time cluster reflecting
its atmospheric and time-conscious lyrical content. We recommend:
1. Herbs — Home Grown (reggae)
2. Lightnin Hopkins — So Long Baby (country)
3. Pet Shop Boys — Always On My Mind (pop)

**Because the user listened to Rage Against the Machine —
Pistol Grip Pump (Cluster 6 — Obscene):**

This song was assigned to the most provocative cluster in the
model with the highest obscene score of any cluster at 0.460.
The recommendations from this cluster match that raw and
unfiltered lyrical energy.

### Conclusion

The KMeans model successfully grouped 28,362 songs into 8 meaningful
clusters based entirely on lyrical content. Without using any genre
labels, artist names, or pre-defined categories the algorithm
discovered natural groupings that align clearly with intuitive
musical and emotional categories — night/time songs, world/life
reflections, violent and intense songs, music about music, sad
songs, romantic songs, obscene and provocative songs, and a
general mixed category.

By assigning the 10 test songs to these clusters we built a
content-based recommendation system that goes beyond surface-level
genre matching. The system connects the user to songs that share
the same lyrical soul as what they already enjoy — regardless of
genre, era, or artist — grounded entirely in the mathematical
structure of the music's lyrical content.