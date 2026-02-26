---
name: Home
assetId: df3b8f2a-02f3-470a-98c6-9712b2eaa5b4
type: page
---

# 🇱🇰 Sri Lanka Government App Survey

Insights from **2,000 survey responses** on citizen preferences for a national government services app.

```sql survey
SELECT
    `What is your gender?` as gender,
    `If you are in Sri Lanka, where do you live?` as location,
    `What is your highest level of education completed?` as education,
    `What is your primary language preference for mobile apps?` as language,
    `What phone do you use?` as phone_type,
    `In the last 2 years, which government office have you visited personally?` as offices_visited,
    `What is the single most frustrating part of dealing with the government?` as frustration,
    `If you could digitize ONE thing immediately, what would it be?` as digitization_priority,
    `What are the government services you would expect to be on the app?` as expected_services,
    `What is the mobile signal strength in your area?` as signal_strength,
    `What do you think about the Gov Pay feature in this app?` as gov_pay_opinion,
    `What kind of name sounds most "Trustworthy" for a Government App?` as app_name_pref,
    `Which color combination do you like the most?` as color_scheme,
    `How would you prefer to log in to this app?` as login_method,
    `How do you feel about the government having all your data (health, vehicle, ID) in one app?` as data_comfort
FROM standardized_survey_data_2000_standardized_survey_data_2000
```

## Overview

{% big_value
    data="survey"
    value="count(*)"
    title="Total Responses"
    fmt="num0"
/%}

{% big_value
    data="survey"
    value="count(distinct location)"
    title="Districts Represented"
    fmt="num0"
/%}

{% big_value
    data="survey"
    value="avg(data_comfort)"
    title="Avg Data Comfort (1-5)"
    fmt="num1"
/%}

{% big_value
    data="survey"
    value="avg(signal_strength)"
    title="Avg Signal Strength (1-5)"
    fmt="num1"
/%}

{% bar_chart
    data="survey"
    x="gender"
    y="count(*)"
    title="Responses by Gender"
    order="count(*) desc"
    y_fmt="num0"
/%}

{% horizontal_bar_chart
    data="survey"
    y="location"
    x="count(*)"
    title="Responses by District"
    y_sort="data"
    order="count(*) desc"
    x_fmt="num0"
/%}

## Government Pain Points & Digitization Priorities

```sql frustration_themes
SELECT
    CASE
        WHEN lower(f) LIKE '%queue%' OR lower(f) LIKE '%wasting time%' THEN 'Standing in queues / Wasting time'
        WHEN lower(f) LIKE '%multiple counter%' OR lower(f) LIKE '%multiple office%' OR lower(f) LIKE '%multiple building%' THEN 'Multiple counters/offices for one task'
        WHEN lower(f) LIKE '%status%' OR lower(f) LIKE '%ready%' OR lower(f) LIKE '%still have to wait%' THEN 'Not knowing application status'
        WHEN lower(f) LIKE '%rude%' OR lower(f) LIKE '%unhelpful%' OR lower(f) LIKE '%harsh%' THEN 'Rude / unhelpful staff'
        WHEN lower(f) LIKE '%where to go%' OR lower(f) LIKE '%confusion%' OR lower(f) LIKE '%exact location%' OR lower(f) LIKE '%confused%' THEN 'Not knowing where to go'
        ELSE 'Other'
    END as theme,
    count(*) as mentions
FROM standardized_survey_data_2000_standardized_survey_data_2000
ARRAY JOIN
    arrayFilter(x -> x != '',
        arrayMap(x -> trim(x),
            splitByString(',', assumeNotNull(`What is the single most frustrating part of dealing with the government?`))
        )
    ) as f
GROUP BY theme
ORDER BY mentions DESC
```

{% horizontal_bar_chart
    data="frustration_themes"
    y="theme"
    x="mentions"
    title="Top Frustrations with Government Services"
    subtitle="Multi-select responses split into themes (total mentions)"
    y_sort="data"
    x_fmt="num0"
/%}

```sql digitization_priorities
SELECT
    CASE
        WHEN lower(d) LIKE '%digital wallet%' OR lower(d) LIKE '%digital vault%' OR lower(d) LIKE '%document%' THEN 'Digital document wallet'
        WHEN lower(d) LIKE '%vehicle%' OR lower(d) LIKE '%license%' THEN 'Vehicle license renewal'
        WHEN lower(d) LIKE '%hospital%' OR lower(d) LIKE '%clinic%' OR lower(d) LIKE '%health%' THEN 'Hospital/clinic booking'
        WHEN lower(d) LIKE '%birth%' OR lower(d) LIKE '%certificate%' THEN 'Birth certificate copy'
        WHEN lower(d) LIKE '%police%' OR lower(d) LIKE '%clearance%' THEN 'Police clearance report'
        ELSE 'Other'
    END as priority_theme,
    count(*) as mentions
FROM standardized_survey_data_2000_standardized_survey_data_2000
ARRAY JOIN
    arrayFilter(x -> x != '',
        arrayMap(x -> trim(x),
            splitByString(',', assumeNotNull(`If you could digitize ONE thing immediately, what would it be?`))
        )
    ) as d
GROUP BY priority_theme
ORDER BY mentions DESC
```

{% horizontal_bar_chart
    data="digitization_priorities"
    y="priority_theme"
    x="mentions"
    title="What Would You Digitize First?"
    subtitle="Multi-select responses grouped into themes (total mentions)"
    y_sort="data"
    x_fmt="num0"
/%}

## App Preferences

```sql login_preferences
SELECT
    CASE
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%biometric%' OR lower(`How would you prefer to log in to this app?`) LIKE '%face%' OR lower(`How would you prefer to log in to this app?`) LIKE '%fingerprint%' THEN 'Biometrics (FaceID / Fingerprint)'
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%otp%' OR lower(`How would you prefer to log in to this app?`) LIKE '%sms%' THEN 'OTP (SMS)'
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%username%' OR lower(`How would you prefer to log in to this app?`) LIKE '%password%' THEN 'Username & Password'
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%scan%' OR lower(`How would you prefer to log in to this app?`) LIKE '%physical%' OR lower(`How would you prefer to log in to this app?`) LIKE '%nic%' THEN 'Physical ID Scan / NIC'
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%mfa%' OR lower(`How would you prefer to log in to this app?`) LIKE '%multi%' OR lower(`How would you prefer to log in to this app?`) LIKE '%2fa%' OR lower(`How would you prefer to log in to this app?`) LIKE '%two factor%' THEN 'Multi-Factor Auth'
        WHEN `How would you prefer to log in to this app?` IS NULL THEN 'No response'
        ELSE 'Other'
    END as login_category,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
GROUP BY login_category
ORDER BY responses DESC
```

{% horizontal_bar_chart
    data="login_preferences"
    y="login_category"
    x="responses"
    title="Preferred Login Method"
    y_sort="data"
    x_fmt="num0"
/%}

```sql app_name_preferences
SELECT
    CASE
        WHEN lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%modern%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%onelanka%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%citizen+%' THEN 'Modern & Unified (OneLanka / Citizen+)'
        WHEN lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%official%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%descriptive%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%govportal%' THEN 'Official & Descriptive (GovPortal)'
        WHEN lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%traditional%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%heritage%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%serendip%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%janaseva%' THEN 'Traditional / Heritage (Serendip / JanaSeva)'
        WHEN lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%short%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%catchy%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%+94%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%liolk%' THEN 'Short & Catchy (+94 / LioLK)'
        WHEN lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%friendly%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%niyamai%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%saho%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%simple%' THEN 'Friendly / Casual (Niyamai / Saho)'
        WHEN `What kind of name sounds most "Trustworthy" for a Government App?` IS NULL THEN 'No response'
        ELSE 'Other suggestions'
    END as name_category,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
GROUP BY name_category
ORDER BY responses DESC
```

{% horizontal_bar_chart
    data="app_name_preferences"
    y="name_category"
    x="responses"
    title="Most Trustworthy App Name Style"
    y_sort="data"
    x_fmt="num0"
/%}

{% bar_chart
    data="survey"
    x="color_scheme"
    y="count(*)"
    title="Preferred Color Scheme"
    order="count(*) desc"
    y_fmt="num0"
/%}

```sql data_comfort_dist
SELECT
    data_comfort as comfort_rating,
    CASE
        WHEN data_comfort = 1 THEN '1 - Very Uncomfortable'
        WHEN data_comfort = 2 THEN '2 - Uncomfortable'
        WHEN data_comfort = 3 THEN '3 - Neutral'
        WHEN data_comfort = 4 THEN '4 - Comfortable'
        WHEN data_comfort = 5 THEN '5 - Very Comfortable'
    END as label,
    count(*) as responses
FROM (
    SELECT `How do you feel about the government having all your data (health, vehicle, ID) in one app?` as data_comfort
    FROM standardized_survey_data_2000_standardized_survey_data_2000
    WHERE data_comfort IS NOT NULL
)
GROUP BY data_comfort
ORDER BY data_comfort
```

{% bar_chart
    data="data_comfort_dist"
    x="label"
    y="responses"
    title="Comfort with Government Holding All Data in One App"
    subtitle="1 = Very Uncomfortable, 5 = Very Comfortable"
    y_fmt="num0"
/%}

## Demographics Deep Dive

```sql education_grouped
SELECT
    CASE
        WHEN education IN ('Bachelors') THEN 'Bachelors'
        WHEN education IN ('Masters') THEN 'Masters'
        WHEN education IN ('A/L') THEN 'A/L'
        WHEN education IN ('O/L') THEN 'O/L'
        WHEN lower(education) LIKE '%phd%' OR lower(education) LIKE '%dba%' THEN 'PhD / Doctorate'
        WHEN lower(education) LIKE '%diploma%' OR lower(education) LIKE '%hnd%' OR lower(education) LIKE '%nvq%' OR lower(education) LIKE '%higher national%' THEN 'Diploma / HND'
        WHEN education IS NULL THEN 'No response'
        ELSE 'Other'
    END as education_level,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
GROUP BY education_level
ORDER BY responses DESC
```

{% bar_chart
    data="education_grouped"
    x="education_level"
    y="responses"
    title="Education Level"
    y_fmt="num0"
/%}

{% bar_chart
    data="survey"
    x="phone_type"
    y="count(*)"
    title="Phone Type"
    order="count(*) desc"
    y_fmt="num0"
/%}

{% bar_chart
    data="survey"
    x="language"
    y="count(*)"
    title="Preferred App Language"
    order="count(*) desc"
    y_fmt="num0"
/%}

## Expected Government Services — Text Analysis

_What services do citizens expect on the app? This section analyzes the free-text responses to uncover the most common keywords, phrases, and service themes._

```sql top_keywords
SELECT
    w as keyword,
    count(*) as frequency
FROM standardized_survey_data_2000_standardized_survey_data_2000
ARRAY JOIN
    arrayFilter(x -> length(x) > 2 AND x NOT IN (
        'the', 'and', 'for', 'are', 'that', 'this', 'with', 'from', 'have', 'has',
        'was', 'were', 'will', 'can', 'could', 'would', 'should', 'may', 'might',
        'all', 'not', 'but', 'they', 'their', 'them', 'been', 'being', 'its',
        'also', 'than', 'other', 'into', 'some', 'such', 'like', 'just', 'more',
        'very', 'most', 'any', 'each', 'every', 'about', 'which', 'when', 'where',
        'what', 'how', 'who', 'whom', 'why', 'one', 'two', 'get', 'got', 'etc',
        'app', 'able', 'need', 'want', 'make', 'use', 'let', 'via', 'per', 'our',
        'you', 'your', 'out', 'above', 'expect', 'services', 'service', 'government',
        'should', 'possible', 'think', 'things', 'thing', 'related', 'mentioned',
        'including', 'include', 'included', 'everything', 'anything', 'something',
        'must', 'well', 'good', 'same', 'new', 'way', 'those', 'these', 'only',
        'through', 'over', 'under', 'between', 'after', 'before', 'during', 'without',
        'there', 'relevant', 'almost', 'having', 'see'
    ),
        arrayMap(x -> trim(replaceRegexpAll(lower(x), '[^a-z]', '')),
            splitByRegexp('[ ,\n\r\t/]+', assumeNotNull(`What are the government services you would expect to be on the app?`))
        )
    ) as w
WHERE `What are the government services you would expect to be on the app?` IS NOT NULL
    AND `What are the government services you would expect to be on the app?` != ''
GROUP BY w
HAVING frequency >= 15
ORDER BY frequency DESC
```

{% horizontal_bar_chart
    data="top_keywords"
    y="keyword"
    x="frequency"
    title="Most Common Keywords in Expected Services"
    subtitle="Stop words removed — showing words mentioned 15+ times across 2,000 responses"
    y_sort="data"
    x_fmt="num0"
/%}

```sql common_phrases
SELECT
    concat(w1, ' ', w2) as phrase,
    count(*) as frequency
FROM (
    SELECT
        arrayMap(x -> trim(replaceRegexpAll(lower(x), '[^a-z]', '')),
            splitByRegexp('[ ,\n\r\t/]+', assumeNotNull(`What are the government services you would expect to be on the app?`))
        ) as words
    FROM standardized_survey_data_2000_standardized_survey_data_2000
    WHERE `What are the government services you would expect to be on the app?` IS NOT NULL
        AND `What are the government services you would expect to be on the app?` != ''
)
ARRAY JOIN
    arraySlice(words, 1, length(words) - 1) as w1,
    arraySlice(words, 2) as w2
WHERE length(w1) > 2 AND length(w2) > 2
    AND w1 NOT IN ('the', 'and', 'for', 'are', 'all', 'its', 'can', 'has', 'was')
    AND w2 NOT IN ('the', 'and', 'for', 'are', 'all', 'its', 'can', 'has', 'was')
GROUP BY phrase
HAVING frequency >= 12
ORDER BY frequency DESC
```

{% horizontal_bar_chart
    data="common_phrases"
    y="phrase"
    x="frequency"
    title="Most Common Two-Word Phrases"
    subtitle="Adjacent word pairs appearing 12+ times — reveals specific service demands"
    y_sort="data"
    x_fmt="num0"
/%}

```sql service_categories
SELECT
    CASE
        WHEN lower(s) LIKE '%vehicle%' OR lower(s) LIKE '%license%' OR lower(s) LIKE '%licence%' OR lower(s) LIKE '%dmt%' OR lower(s) LIKE '%driving%' THEN 'Vehicle & Driving License'
        WHEN lower(s) LIKE '%hospital%' OR lower(s) LIKE '%health%' OR lower(s) LIKE '%medical%' OR lower(s) LIKE '%clinic%' THEN 'Healthcare'
        WHEN lower(s) LIKE '%police%' OR lower(s) LIKE '%clearance%' OR lower(s) LIKE '%traffic%' OR lower(s) LIKE '%fine%' THEN 'Police & Traffic'
        WHEN lower(s) LIKE '%birth%' OR lower(s) LIKE '%death%' OR lower(s) LIKE '%marriage%' OR lower(s) LIKE '%certificate%' THEN 'Civil Registration'
        WHEN lower(s) LIKE '%passport%' OR lower(s) LIKE '%immigration%' THEN 'Passport & Immigration'
        WHEN lower(s) LIKE '%transport%' OR lower(s) LIKE '%bus%' OR lower(s) LIKE '%train%' OR lower(s) LIKE '%railway%' THEN 'Public Transport'
        WHEN lower(s) LIKE '%nic%' OR lower(s) LIKE '%identity%' OR lower(s) LIKE '%id card%' OR lower(s) LIKE '%national id%' THEN 'National ID (NIC)'
        WHEN lower(s) LIKE '%payment%' OR lower(s) LIKE '%pay %' OR lower(s) LIKE '%digital wallet%' THEN 'Digital Payments'
        WHEN lower(s) LIKE '%tax%' OR lower(s) LIKE '%revenue%' OR lower(s) LIKE '%epf%' OR lower(s) LIKE '%etf%' THEN 'Tax & Revenue'
        WHEN lower(s) LIKE '%grama%' OR lower(s) LIKE '%divisional%' OR lower(s) LIKE '%secretariat%' OR lower(s) LIKE '%niladhari%' OR lower(s) LIKE '%niladari%' THEN 'Grama Niladhari / Divisional'
        WHEN lower(s) LIKE '%water%' OR lower(s) LIKE '%electric%' OR lower(s) LIKE '%utility%' OR lower(s) LIKE '%bill%' THEN 'Utility Payments'
        WHEN lower(s) LIKE '%land%' OR lower(s) LIKE '%property%' OR lower(s) LIKE '%deed%' OR lower(s) LIKE '%registry%' THEN 'Land & Property'
        WHEN lower(s) LIKE '%education%' OR lower(s) LIKE '%school%' OR lower(s) LIKE '%exam%' OR lower(s) LIKE '%university%' THEN 'Education'
        WHEN lower(s) LIKE '%business%' OR lower(s) LIKE '%company%' THEN 'Business Registration'
        ELSE 'Other'
    END as service_category,
    count(*) as mentions
FROM standardized_survey_data_2000_standardized_survey_data_2000
ARRAY JOIN
    arrayFilter(x -> x != '',
        arrayMap(x -> trim(x),
            splitByString(',', assumeNotNull(`What are the government services you would expect to be on the app?`))
        )
    ) as s
WHERE `What are the government services you would expect to be on the app?` IS NOT NULL
GROUP BY service_category
HAVING service_category != 'Other'
ORDER BY mentions DESC
```

{% horizontal_bar_chart
    data="service_categories"
    y="service_category"
    x="mentions"
    title="Expected Services by Theme"
    subtitle="Free-text responses categorized into service domains"
    y_sort="data"
    x_fmt="num0"
/%}

{% table data="top_keywords" /%}

{% table data="common_phrases" /%}

## Cross-Tabulations

_How do service expectations and frustrations vary across demographics? These charts break down the data by district, education, gender, and phone type._

### Expected Services by District

```sql services_by_district
SELECT
    `If you are in Sri Lanka, where do you live?` as location,
    CASE
        WHEN lower(s) LIKE '%vehicle%' OR lower(s) LIKE '%license%' OR lower(s) LIKE '%licence%' OR lower(s) LIKE '%driving%' THEN 'Vehicle & Driving License'
        WHEN lower(s) LIKE '%hospital%' OR lower(s) LIKE '%health%' OR lower(s) LIKE '%medical%' OR lower(s) LIKE '%clinic%' THEN 'Healthcare'
        WHEN lower(s) LIKE '%police%' OR lower(s) LIKE '%clearance%' OR lower(s) LIKE '%traffic%' OR lower(s) LIKE '%fine%' THEN 'Police & Traffic'
        WHEN lower(s) LIKE '%birth%' OR lower(s) LIKE '%death%' OR lower(s) LIKE '%marriage%' OR lower(s) LIKE '%certificate%' THEN 'Civil Registration'
        WHEN lower(s) LIKE '%passport%' OR lower(s) LIKE '%immigration%' THEN 'Passport & Immigration'
        WHEN lower(s) LIKE '%nic%' OR lower(s) LIKE '%identity%' OR lower(s) LIKE '%id card%' THEN 'National ID (NIC)'
        WHEN lower(s) LIKE '%tax%' OR lower(s) LIKE '%revenue%' OR lower(s) LIKE '%epf%' THEN 'Tax & Revenue'
        WHEN lower(s) LIKE '%education%' OR lower(s) LIKE '%school%' OR lower(s) LIKE '%exam%' THEN 'Education'
        ELSE NULL
    END as service_category,
    count(*) as mentions
FROM standardized_survey_data_2000_standardized_survey_data_2000
ARRAY JOIN
    arrayFilter(x -> x != '',
        arrayMap(x -> trim(x),
            splitByString(',', assumeNotNull(`What are the government services you would expect to be on the app?`))
        )
    ) as s
WHERE `What are the government services you would expect to be on the app?` IS NOT NULL
    AND service_category IS NOT NULL
    AND `If you are in Sri Lanka, where do you live?` IS NOT NULL
GROUP BY location, service_category
ORDER BY location, mentions DESC
```

{% bar_chart
    data="services_by_district"
    x="location"
    y="mentions"
    series="service_category"
    stacked="100%"
    title="Expected Services by District"
    subtitle="Which services matter most in each district? (% of mentions)"
    x_axis_options={label_rotate=45}
/%}

### Expected Services by Education Level

```sql services_by_education
SELECT
    CASE
        WHEN `What is your highest level of education completed?` IN ('Bachelors') THEN 'Bachelors'
        WHEN `What is your highest level of education completed?` IN ('Masters') THEN 'Masters'
        WHEN `What is your highest level of education completed?` IN ('A/L') THEN 'A/L'
        WHEN `What is your highest level of education completed?` IN ('O/L') THEN 'O/L'
        WHEN lower(`What is your highest level of education completed?`) LIKE '%phd%' OR lower(`What is your highest level of education completed?`) LIKE '%dba%' THEN 'PhD / Doctorate'
        WHEN lower(`What is your highest level of education completed?`) LIKE '%diploma%' OR lower(`What is your highest level of education completed?`) LIKE '%hnd%' THEN 'Diploma / HND'
        ELSE 'Other'
    END as education_level,
    CASE
        WHEN lower(s) LIKE '%vehicle%' OR lower(s) LIKE '%license%' OR lower(s) LIKE '%licence%' OR lower(s) LIKE '%driving%' THEN 'Vehicle & Driving License'
        WHEN lower(s) LIKE '%hospital%' OR lower(s) LIKE '%health%' OR lower(s) LIKE '%medical%' OR lower(s) LIKE '%clinic%' THEN 'Healthcare'
        WHEN lower(s) LIKE '%police%' OR lower(s) LIKE '%clearance%' OR lower(s) LIKE '%traffic%' OR lower(s) LIKE '%fine%' THEN 'Police & Traffic'
        WHEN lower(s) LIKE '%birth%' OR lower(s) LIKE '%death%' OR lower(s) LIKE '%marriage%' OR lower(s) LIKE '%certificate%' THEN 'Civil Registration'
        WHEN lower(s) LIKE '%passport%' OR lower(s) LIKE '%immigration%' THEN 'Passport & Immigration'
        WHEN lower(s) LIKE '%nic%' OR lower(s) LIKE '%identity%' OR lower(s) LIKE '%id card%' THEN 'National ID (NIC)'
        WHEN lower(s) LIKE '%tax%' OR lower(s) LIKE '%revenue%' OR lower(s) LIKE '%epf%' THEN 'Tax & Revenue'
        WHEN lower(s) LIKE '%education%' OR lower(s) LIKE '%school%' OR lower(s) LIKE '%exam%' THEN 'Education'
        ELSE NULL
    END as service_category,
    count(*) as mentions
FROM standardized_survey_data_2000_standardized_survey_data_2000
ARRAY JOIN
    arrayFilter(x -> x != '',
        arrayMap(x -> trim(x),
            splitByString(',', assumeNotNull(`What are the government services you would expect to be on the app?`))
        )
    ) as s
WHERE `What are the government services you would expect to be on the app?` IS NOT NULL
    AND service_category IS NOT NULL
GROUP BY education_level, service_category
ORDER BY education_level, mentions DESC
```

{% bar_chart
    data="services_by_education"
    x="education_level"
    y="mentions"
    series="service_category"
    stacked="100%"
    title="Expected Services by Education Level"
    subtitle="Do priorities shift with education? (% of mentions)"
/%}

### Expected Services by Gender

```sql services_by_gender
SELECT
    `What is your gender?` as gender,
    CASE
        WHEN lower(s) LIKE '%vehicle%' OR lower(s) LIKE '%license%' OR lower(s) LIKE '%licence%' OR lower(s) LIKE '%driving%' THEN 'Vehicle & Driving License'
        WHEN lower(s) LIKE '%hospital%' OR lower(s) LIKE '%health%' OR lower(s) LIKE '%medical%' OR lower(s) LIKE '%clinic%' THEN 'Healthcare'
        WHEN lower(s) LIKE '%police%' OR lower(s) LIKE '%clearance%' OR lower(s) LIKE '%traffic%' OR lower(s) LIKE '%fine%' THEN 'Police & Traffic'
        WHEN lower(s) LIKE '%birth%' OR lower(s) LIKE '%death%' OR lower(s) LIKE '%marriage%' OR lower(s) LIKE '%certificate%' THEN 'Civil Registration'
        WHEN lower(s) LIKE '%passport%' OR lower(s) LIKE '%immigration%' THEN 'Passport & Immigration'
        WHEN lower(s) LIKE '%nic%' OR lower(s) LIKE '%identity%' OR lower(s) LIKE '%id card%' THEN 'National ID (NIC)'
        WHEN lower(s) LIKE '%tax%' OR lower(s) LIKE '%revenue%' OR lower(s) LIKE '%epf%' THEN 'Tax & Revenue'
        WHEN lower(s) LIKE '%education%' OR lower(s) LIKE '%school%' OR lower(s) LIKE '%exam%' THEN 'Education'
        ELSE NULL
    END as service_category,
    count(*) as mentions
FROM standardized_survey_data_2000_standardized_survey_data_2000
ARRAY JOIN
    arrayFilter(x -> x != '',
        arrayMap(x -> trim(x),
            splitByString(',', assumeNotNull(`What are the government services you would expect to be on the app?`))
        )
    ) as s
WHERE `What are the government services you would expect to be on the app?` IS NOT NULL
    AND service_category IS NOT NULL
GROUP BY gender, service_category
ORDER BY gender, mentions DESC
```

{% bar_chart
    data="services_by_gender"
    x="gender"
    y="mentions"
    series="service_category"
    stacked="100%"
    title="Expected Services by Gender"
    subtitle="Any differences between male and female respondents? (% of mentions)"
/%}

### Expected Services by Phone Type

```sql services_by_phone
SELECT
    `What phone do you use?` as phone_type,
    CASE
        WHEN lower(s) LIKE '%vehicle%' OR lower(s) LIKE '%license%' OR lower(s) LIKE '%licence%' OR lower(s) LIKE '%driving%' THEN 'Vehicle & Driving License'
        WHEN lower(s) LIKE '%hospital%' OR lower(s) LIKE '%health%' OR lower(s) LIKE '%medical%' OR lower(s) LIKE '%clinic%' THEN 'Healthcare'
        WHEN lower(s) LIKE '%police%' OR lower(s) LIKE '%clearance%' OR lower(s) LIKE '%traffic%' OR lower(s) LIKE '%fine%' THEN 'Police & Traffic'
        WHEN lower(s) LIKE '%birth%' OR lower(s) LIKE '%death%' OR lower(s) LIKE '%marriage%' OR lower(s) LIKE '%certificate%' THEN 'Civil Registration'
        WHEN lower(s) LIKE '%passport%' OR lower(s) LIKE '%immigration%' THEN 'Passport & Immigration'
        WHEN lower(s) LIKE '%nic%' OR lower(s) LIKE '%identity%' OR lower(s) LIKE '%id card%' THEN 'National ID (NIC)'
        WHEN lower(s) LIKE '%tax%' OR lower(s) LIKE '%revenue%' OR lower(s) LIKE '%epf%' THEN 'Tax & Revenue'
        WHEN lower(s) LIKE '%education%' OR lower(s) LIKE '%school%' OR lower(s) LIKE '%exam%' THEN 'Education'
        ELSE NULL
    END as service_category,
    count(*) as mentions
FROM standardized_survey_data_2000_standardized_survey_data_2000
ARRAY JOIN
    arrayFilter(x -> x != '',
        arrayMap(x -> trim(x),
            splitByString(',', assumeNotNull(`What are the government services you would expect to be on the app?`))
        )
    ) as s
WHERE `What are the government services you would expect to be on the app?` IS NOT NULL
    AND service_category IS NOT NULL
    AND `What phone do you use?` IS NOT NULL
GROUP BY phone_type, service_category
ORDER BY phone_type, mentions DESC
```

{% bar_chart
    data="services_by_phone"
    x="phone_type"
    y="mentions"
    series="service_category"
    stacked="100%"
    title="Expected Services by Phone Type"
    subtitle="Do Android and iPhone users want different things? (% of mentions)"
/%}

### Frustrations by District

```sql frustrations_by_district
SELECT
    `If you are in Sri Lanka, where do you live?` as location,
    CASE
        WHEN lower(f) LIKE '%queue%' OR lower(f) LIKE '%wasting time%' THEN 'Queues / Wasting time'
        WHEN lower(f) LIKE '%multiple counter%' OR lower(f) LIKE '%multiple office%' OR lower(f) LIKE '%multiple building%' THEN 'Multiple counters/offices'
        WHEN lower(f) LIKE '%status%' OR lower(f) LIKE '%ready%' OR lower(f) LIKE '%still have to wait%' THEN 'Not knowing status'
        WHEN lower(f) LIKE '%rude%' OR lower(f) LIKE '%unhelpful%' OR lower(f) LIKE '%harsh%' THEN 'Rude / unhelpful staff'
        WHEN lower(f) LIKE '%where to go%' OR lower(f) LIKE '%confusion%' OR lower(f) LIKE '%exact location%' OR lower(f) LIKE '%confused%' THEN 'Not knowing where to go'
        ELSE NULL
    END as theme,
    count(*) as mentions
FROM standardized_survey_data_2000_standardized_survey_data_2000
ARRAY JOIN
    arrayFilter(x -> x != '',
        arrayMap(x -> trim(x),
            splitByString(',', assumeNotNull(`What is the single most frustrating part of dealing with the government?`))
        )
    ) as f
WHERE `What is the single most frustrating part of dealing with the government?` IS NOT NULL
    AND theme IS NOT NULL
    AND `If you are in Sri Lanka, where do you live?` IS NOT NULL
GROUP BY location, theme
ORDER BY location, mentions DESC
```

{% bar_chart
    data="frustrations_by_district"
    x="location"
    y="mentions"
    series="theme"
    stacked="100%"
    title="Frustrations by District"
    subtitle="Are pain points regional or universal? (% of mentions)"
    x_axis_options={label_rotate=45}
/%}

### Frustrations by Education Level

```sql frustrations_by_education
SELECT
    CASE
        WHEN `What is your highest level of education completed?` IN ('Bachelors') THEN 'Bachelors'
        WHEN `What is your highest level of education completed?` IN ('Masters') THEN 'Masters'
        WHEN `What is your highest level of education completed?` IN ('A/L') THEN 'A/L'
        WHEN `What is your highest level of education completed?` IN ('O/L') THEN 'O/L'
        WHEN lower(`What is your highest level of education completed?`) LIKE '%phd%' OR lower(`What is your highest level of education completed?`) LIKE '%dba%' THEN 'PhD / Doctorate'
        WHEN lower(`What is your highest level of education completed?`) LIKE '%diploma%' OR lower(`What is your highest level of education completed?`) LIKE '%hnd%' THEN 'Diploma / HND'
        ELSE 'Other'
    END as education_level,
    CASE
        WHEN lower(f) LIKE '%queue%' OR lower(f) LIKE '%wasting time%' THEN 'Queues / Wasting time'
        WHEN lower(f) LIKE '%multiple counter%' OR lower(f) LIKE '%multiple office%' OR lower(f) LIKE '%multiple building%' THEN 'Multiple counters/offices'
        WHEN lower(f) LIKE '%status%' OR lower(f) LIKE '%ready%' OR lower(f) LIKE '%still have to wait%' THEN 'Not knowing status'
        WHEN lower(f) LIKE '%rude%' OR lower(f) LIKE '%unhelpful%' OR lower(f) LIKE '%harsh%' THEN 'Rude / unhelpful staff'
        WHEN lower(f) LIKE '%where to go%' OR lower(f) LIKE '%confusion%' OR lower(f) LIKE '%exact location%' OR lower(f) LIKE '%confused%' THEN 'Not knowing where to go'
        ELSE NULL
    END as theme,
    count(*) as mentions
FROM standardized_survey_data_2000_standardized_survey_data_2000
ARRAY JOIN
    arrayFilter(x -> x != '',
        arrayMap(x -> trim(x),
            splitByString(',', assumeNotNull(`What is the single most frustrating part of dealing with the government?`))
        )
    ) as f
WHERE `What is the single most frustrating part of dealing with the government?` IS NOT NULL
    AND theme IS NOT NULL
GROUP BY education_level, theme
ORDER BY education_level, mentions DESC
```

{% bar_chart
    data="frustrations_by_education"
    x="education_level"
    y="mentions"
    series="theme"
    stacked="100%"
    title="Frustrations by Education Level"
    subtitle="Do frustrations differ by education? (% of mentions)"
/%}

### Frustrations by Gender

```sql frustrations_by_gender
SELECT
    `What is your gender?` as gender,
    CASE
        WHEN lower(f) LIKE '%queue%' OR lower(f) LIKE '%wasting time%' THEN 'Queues / Wasting time'
        WHEN lower(f) LIKE '%multiple counter%' OR lower(f) LIKE '%multiple office%' OR lower(f) LIKE '%multiple building%' THEN 'Multiple counters/offices'
        WHEN lower(f) LIKE '%status%' OR lower(f) LIKE '%ready%' OR lower(f) LIKE '%still have to wait%' THEN 'Not knowing status'
        WHEN lower(f) LIKE '%rude%' OR lower(f) LIKE '%unhelpful%' OR lower(f) LIKE '%harsh%' THEN 'Rude / unhelpful staff'
        WHEN lower(f) LIKE '%where to go%' OR lower(f) LIKE '%confusion%' OR lower(f) LIKE '%exact location%' OR lower(f) LIKE '%confused%' THEN 'Not knowing where to go'
        ELSE NULL
    END as theme,
    count(*) as mentions
FROM standardized_survey_data_2000_standardized_survey_data_2000
ARRAY JOIN
    arrayFilter(x -> x != '',
        arrayMap(x -> trim(x),
            splitByString(',', assumeNotNull(`What is the single most frustrating part of dealing with the government?`))
        )
    ) as f
WHERE `What is the single most frustrating part of dealing with the government?` IS NOT NULL
    AND theme IS NOT NULL
    AND `What is your gender?` IS NOT NULL
GROUP BY gender, theme
ORDER BY gender, mentions DESC
```

{% bar_chart
    data="frustrations_by_gender"
    x="gender"
    y="mentions"
    series="theme"
    stacked="100%"
    title="Frustrations by Gender"
    subtitle="Do men and women experience different pain points? (% of mentions)"
/%}

### Login Preferences by District

```sql login_by_district
SELECT
    `If you are in Sri Lanka, where do you live?` as location,
    CASE
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%biometric%' OR lower(`How would you prefer to log in to this app?`) LIKE '%face%' OR lower(`How would you prefer to log in to this app?`) LIKE '%fingerprint%' THEN 'Biometrics'
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%otp%' OR lower(`How would you prefer to log in to this app?`) LIKE '%sms%' THEN 'OTP (SMS)'
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%username%' OR lower(`How would you prefer to log in to this app?`) LIKE '%password%' THEN 'Username & Password'
        WHEN `How would you prefer to log in to this app?` IS NULL THEN NULL
        ELSE 'Other'
    END as login_category,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `If you are in Sri Lanka, where do you live?` IS NOT NULL
    AND login_category IS NOT NULL
GROUP BY location, login_category
ORDER BY location, responses DESC
```

{% bar_chart
    data="login_by_district"
    x="location"
    y="responses"
    series="login_category"
    stacked="100%"
    title="Login Preferences by District"
    subtitle="Does login method preference vary by region? (% of responses)"
    x_axis_options={label_rotate=45}
/%}

### Data Comfort by District

```sql comfort_by_district
SELECT
    `If you are in Sri Lanka, where do you live?` as location,
    CASE
        WHEN `How do you feel about the government having all your data (health, vehicle, ID) in one app?` <= 2 THEN 'Uncomfortable (1-2)'
        WHEN `How do you feel about the government having all your data (health, vehicle, ID) in one app?` = 3 THEN 'Neutral (3)'
        WHEN `How do you feel about the government having all your data (health, vehicle, ID) in one app?` >= 4 THEN 'Comfortable (4-5)'
    END as comfort_group,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `How do you feel about the government having all your data (health, vehicle, ID) in one app?` IS NOT NULL
    AND `If you are in Sri Lanka, where do you live?` IS NOT NULL
GROUP BY location, comfort_group
ORDER BY location, comfort_group
```

{% bar_chart
    data="comfort_by_district"
    x="location"
    y="responses"
    series="comfort_group"
    stacked="100%"
    title="Data Comfort by District"
    subtitle="How comfortable are citizens with government holding all data? (% of responses)"
    x_axis_options={label_rotate=45}
/%}
