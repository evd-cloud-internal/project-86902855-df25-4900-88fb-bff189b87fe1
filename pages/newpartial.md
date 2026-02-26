---
name: newpartial
assetId: 94d98d5e-fc57-4f18-98a4-579f24dc40dd
type: partial
---

# 🧑 Male Respondents — Full Survey Analysis

_A comprehensive look at what **1,617 male respondents** (81% of the survey) said across all survey questions._

```sql male_summary
SELECT
    count(*) as total_male,
    round(count(*) * 100.0 / (SELECT count(*) FROM standardized_survey_data_2000_standardized_survey_data_2000), 1) as pct_of_total,
    round(avg(`How do you feel about the government having all your data (health, vehicle, ID) in one app?`), 2) as avg_data_comfort,
    round(avg(`What do you think about the Gov Pay feature in this app?`), 2) as avg_govpay_rating,
    round(avg(`What is the mobile signal strength in your area?`), 2) as avg_signal_strength
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Male'
```

{% big_value
    data="male_summary"
    value="total_male"
    title="Male Respondents"
    fmt="num0"
/%}

{% big_value
    data="male_summary"
    value="avg_data_comfort"
    title="Avg Data Comfort (1-5)"
    fmt="num1"
/%}

{% big_value
    data="male_summary"
    value="avg_govpay_rating"
    title="Avg GovPay Rating (1-5)"
    fmt="num1"
/%}

{% big_value
    data="male_summary"
    value="avg_signal_strength"
    title="Avg Signal Strength (1-5)"
    fmt="num1"
/%}

## Demographics

```sql male_by_district
SELECT
    `If you are in Sri Lanka, where do you live?` as district,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Male'
    AND `If you are in Sri Lanka, where do you live?` IS NOT NULL
GROUP BY district
ORDER BY responses DESC
```

{% bar_chart
    data="male_by_district"
    x="district"
    y="responses"
    title="Male Respondents by District"
    y_fmt="num0"
    x_axis_options={label_rotate=45}
/%}

```sql male_by_education
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
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Male'
GROUP BY education_level
ORDER BY responses DESC
```

{% bar_chart
    data="male_by_education"
    x="education_level"
    y="responses"
    title="Male Respondents by Education Level"
    y_fmt="num0"
/%}

```sql male_by_phone
SELECT
    `What phone do you use?` as phone_type,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Male'
    AND `What phone do you use?` IS NOT NULL
GROUP BY phone_type
ORDER BY responses DESC
```

{% bar_chart
    data="male_by_phone"
    x="phone_type"
    y="responses"
    title="Male Respondents by Phone Type"
    y_fmt="num0"
/%}

## What Frustrates Men Most?

```sql male_frustrations
SELECT
    CASE
        WHEN lower(f) LIKE '%queue%' OR lower(f) LIKE '%wasting time%' OR lower(f) LIKE '%long time%' OR lower(f) LIKE '%waiting%' THEN 'Queues / Long waits'
        WHEN lower(f) LIKE '%multiple counter%' OR lower(f) LIKE '%multiple office%' OR lower(f) LIKE '%multiple building%' OR lower(f) LIKE '%many place%' THEN 'Multiple counters/offices'
        WHEN lower(f) LIKE '%status%' OR lower(f) LIKE '%ready%' OR lower(f) LIKE '%still have to wait%' OR lower(f) LIKE '%tracking%' THEN 'Not knowing status'
        WHEN lower(f) LIKE '%rude%' OR lower(f) LIKE '%unhelpful%' OR lower(f) LIKE '%harsh%' OR lower(f) LIKE '%attitude%' THEN 'Rude / unhelpful staff'
        WHEN lower(f) LIKE '%where to go%' OR lower(f) LIKE '%confusion%' OR lower(f) LIKE '%exact location%' OR lower(f) LIKE '%confused%' OR lower(f) LIKE '%unclear%' THEN 'Not knowing where to go'
        WHEN lower(f) LIKE '%corrupt%' OR lower(f) LIKE '%bribe%' THEN 'Corruption / Bribes'
        WHEN lower(f) LIKE '%online%' OR lower(f) LIKE '%digital%' OR lower(f) LIKE '%website%' THEN 'Lack of digital services'
        WHEN lower(f) LIKE '%paper%' OR lower(f) LIKE '%document%' OR lower(f) LIKE '%form%' THEN 'Paperwork / Documents'
        ELSE NULL
    END as frustration_theme,
    count(*) as mentions
FROM standardized_survey_data_2000_standardized_survey_data_2000
ARRAY JOIN
    arrayFilter(x -> x != '',
        arrayMap(x -> trim(x),
            splitByString(',', assumeNotNull(`What is the single most frustrating part of dealing with the government?`))
        )
    ) as f
WHERE `What is your gender?` = 'Male'
    AND `What is the single most frustrating part of dealing with the government?` IS NOT NULL
    AND frustration_theme IS NOT NULL
GROUP BY frustration_theme
ORDER BY mentions DESC
```

{% horizontal_bar_chart
    data="male_frustrations"
    y="frustration_theme"
    x="mentions"
    title="Top Frustrations — Male Respondents"
    subtitle="What is the single most frustrating part of dealing with the government?"
    y_sort="data"
    x_fmt="num0"
/%}

## What Services Do Men Want?

```sql male_expected_services
SELECT
    CASE
        WHEN lower(s) LIKE '%vehicle%' OR lower(s) LIKE '%license%' OR lower(s) LIKE '%licence%' OR lower(s) LIKE '%driving%' THEN 'Vehicle & Driving License'
        WHEN lower(s) LIKE '%hospital%' OR lower(s) LIKE '%health%' OR lower(s) LIKE '%medical%' OR lower(s) LIKE '%clinic%' THEN 'Healthcare'
        WHEN lower(s) LIKE '%police%' OR lower(s) LIKE '%clearance%' OR lower(s) LIKE '%traffic%' OR lower(s) LIKE '%fine%' THEN 'Police & Traffic'
        WHEN lower(s) LIKE '%birth%' OR lower(s) LIKE '%death%' OR lower(s) LIKE '%marriage%' OR lower(s) LIKE '%certificate%' THEN 'Civil Registration'
        WHEN lower(s) LIKE '%passport%' OR lower(s) LIKE '%immigration%' THEN 'Passport & Immigration'
        WHEN lower(s) LIKE '%nic%' OR lower(s) LIKE '%identity%' OR lower(s) LIKE '%id card%' THEN 'National ID (NIC)'
        WHEN lower(s) LIKE '%tax%' OR lower(s) LIKE '%revenue%' OR lower(s) LIKE '%epf%' THEN 'Tax & Revenue'
        WHEN lower(s) LIKE '%education%' OR lower(s) LIKE '%school%' OR lower(s) LIKE '%exam%' THEN 'Education'
        WHEN lower(s) LIKE '%transport%' OR lower(s) LIKE '%bus%' OR lower(s) LIKE '%train%' THEN 'Public Transport'
        WHEN lower(s) LIKE '%water%' OR lower(s) LIKE '%electric%' OR lower(s) LIKE '%utility%' OR lower(s) LIKE '%bill%' THEN 'Utility Payments'
        WHEN lower(s) LIKE '%land%' OR lower(s) LIKE '%property%' OR lower(s) LIKE '%deed%' THEN 'Land & Property'
        WHEN lower(s) LIKE '%grama%' OR lower(s) LIKE '%divisional%' OR lower(s) LIKE '%secretariat%' OR lower(s) LIKE '%niladhari%' THEN 'Grama Niladhari / Divisional'
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
WHERE `What is your gender?` = 'Male'
    AND `What are the government services you would expect to be on the app?` IS NOT NULL
    AND service_category IS NOT NULL
GROUP BY service_category
ORDER BY mentions DESC
```

{% horizontal_bar_chart
    data="male_expected_services"
    y="service_category"
    x="mentions"
    title="Expected Government Services — Male Respondents"
    subtitle="What services do men want on the app?"
    y_sort="data"
    x_fmt="num0"
/%}

## If You Could Digitize ONE Thing...

```sql male_digitize_one
SELECT
    CASE
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%nic%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%identity%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%id card%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%national id%' THEN 'National ID (NIC)'
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%license%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%licence%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%driving%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%vehicle%' THEN 'Vehicle & Driving License'
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%hospital%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%health%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%medical%' THEN 'Healthcare'
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%birth%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%death%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%marriage%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%certificate%' THEN 'Civil Registration'
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%police%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%clearance%' THEN 'Police Clearance'
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%passport%' THEN 'Passport'
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%tax%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%revenue%' THEN 'Tax & Revenue'
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%land%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%property%' THEN 'Land & Property'
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%grama%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%divisional%' THEN 'Grama Niladhari'
        ELSE NULL
    END as digitize_category,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Male'
    AND `If you could digitize ONE thing immediately, what would it be?` IS NOT NULL
    AND digitize_category IS NOT NULL
GROUP BY digitize_category
ORDER BY responses DESC
```

{% horizontal_bar_chart
    data="male_digitize_one"
    y="digitize_category"
    x="responses"
    title="If You Could Digitize ONE Thing — Male Respondents"
    subtitle="The single most urgent digitization priority"
    y_sort="data"
    x_fmt="num0"
/%}

## App Preferences

### Login Method

```sql male_login
SELECT
    CASE
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%biometric%' OR lower(`How would you prefer to log in to this app?`) LIKE '%face%' OR lower(`How would you prefer to log in to this app?`) LIKE '%fingerprint%' THEN 'Biometrics (Face/Fingerprint)'
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%otp%' OR lower(`How would you prefer to log in to this app?`) LIKE '%sms%' THEN 'OTP (SMS)'
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%username%' OR lower(`How would you prefer to log in to this app?`) LIKE '%password%' THEN 'Username & Password'
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%scan%' OR lower(`How would you prefer to log in to this app?`) LIKE '%barcode%' OR lower(`How would you prefer to log in to this app?`) LIKE '%physical id%' THEN 'Physical ID Scan'
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%mfa%' OR lower(`How would you prefer to log in to this app?`) LIKE '%multi%' OR lower(`How would you prefer to log in to this app?`) LIKE '%two factor%' OR lower(`How would you prefer to log in to this app?`) LIKE '%2fa%' THEN 'Multi-Factor Auth'
        ELSE 'Other'
    END as login_method,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Male'
    AND `How would you prefer to log in to this app?` IS NOT NULL
GROUP BY login_method
ORDER BY responses DESC
```

{% bar_chart
    data="male_login"
    x="login_method"
    y="responses"
    title="Preferred Login Method — Male Respondents"
    y_fmt="num0"
/%}

### App Name Preference

```sql male_app_name
SELECT
    CASE
        WHEN lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%modern%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%onelanka%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%citizen+%' THEN 'Modern & Unified (OneLanka / Citizen+)'
        WHEN lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%official%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%govportal%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%descriptive%' THEN 'Official & Descriptive (GovPortal)'
        WHEN lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%traditional%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%heritage%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%serendip%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%janaseva%' THEN 'Traditional / Heritage'
        WHEN lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%short%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%catchy%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%+94%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%liolk%' THEN 'Short & Catchy (+94 / LioLK)'
        WHEN lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%fun%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%friendly%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%niyamai%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%saho%' THEN 'Fun / Friendly (Niyamai / Saho)'
        ELSE 'Other / Custom Suggestion'
    END as name_preference,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Male'
    AND `What kind of name sounds most "Trustworthy" for a Government App?` IS NOT NULL
GROUP BY name_preference
ORDER BY responses DESC
```

{% bar_chart
    data="male_app_name"
    x="name_preference"
    y="responses"
    title="Preferred App Name Style — Male Respondents"
    y_fmt="num0"
/%}

### Color Preference

```sql male_color
SELECT
    `Which color combination do you like the most?` as color_choice,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Male'
    AND `Which color combination do you like the most?` IS NOT NULL
GROUP BY color_choice
ORDER BY responses DESC
```

{% bar_chart
    data="male_color"
    x="color_choice"
    y="responses"
    title="Preferred Color Combination — Male Respondents"
    y_fmt="num0"
/%}

## Ratings & Sentiment

```sql male_ratings
SELECT
    'Data Comfort' as rating_type,
    `How do you feel about the government having all your data (health, vehicle, ID) in one app?` as score,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Male'
    AND `How do you feel about the government having all your data (health, vehicle, ID) in one app?` IS NOT NULL
GROUP BY rating_type, score
UNION ALL
SELECT
    'GovPay Feature' as rating_type,
    `What do you think about the Gov Pay feature in this app?` as score,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Male'
    AND `What do you think about the Gov Pay feature in this app?` IS NOT NULL
GROUP BY rating_type, score
UNION ALL
SELECT
    'Signal Strength' as rating_type,
    `What is the mobile signal strength in your area?` as score,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Male'
    AND `What is the mobile signal strength in your area?` IS NOT NULL
GROUP BY rating_type, score
ORDER BY rating_type, score
```

{% bar_chart
    data="male_ratings"
    x="score"
    y="responses"
    series="rating_type"
    title="Rating Distributions — Male Respondents (1-5 Scale)"
    subtitle="Data Comfort, GovPay Feature, and Signal Strength"
    y_fmt="num0"
/%}

## Desired Alerts & Reminders

```sql male_alerts
SELECT
    CASE
        WHEN lower(a) LIKE '%license%' OR lower(a) LIKE '%licence%' OR lower(a) LIKE '%vehicle%' OR lower(a) LIKE '%revenue%' THEN 'License/Vehicle Renewals'
        WHEN lower(a) LIKE '%status%' OR lower(a) LIKE '%update%' OR lower(a) LIKE '%progress%' THEN 'Application Status Updates'
        WHEN lower(a) LIKE '%payment%' OR lower(a) LIKE '%bill%' OR lower(a) LIKE '%due%' THEN 'Payment/Bill Reminders'
        WHEN lower(a) LIKE '%fine%' OR lower(a) LIKE '%traffic%' OR lower(a) LIKE '%violation%' THEN 'Fine/Traffic Alerts'
        WHEN lower(a) LIKE '%emergency%' OR lower(a) LIKE '%disaster%' OR lower(a) LIKE '%weather%' THEN 'Emergency/Disaster Alerts'
        WHEN lower(a) LIKE '%expir%' OR lower(a) LIKE '%renew%' THEN 'Document Expiry Alerts'
        ELSE NULL
    END as alert_category,
    count(*) as mentions
FROM standardized_survey_data_2000_standardized_survey_data_2000
ARRAY JOIN
    arrayFilter(x -> x != '',
        arrayMap(x -> trim(x),
            splitByString(',', assumeNotNull(`Which specific alerts and reminders would you like to receive through the App?`))
        )
    ) as a
WHERE `What is your gender?` = 'Male'
    AND `Which specific alerts and reminders would you like to receive through the App?` IS NOT NULL
    AND alert_category IS NOT NULL
GROUP BY alert_category
ORDER BY mentions DESC
```

{% horizontal_bar_chart
    data="male_alerts"
    y="alert_category"
    x="mentions"
    title="Desired Alerts & Reminders — Male Respondents"
    subtitle="What notifications do men want from the app?"
    y_sort="data"
    x_fmt="num0"
/%}

## UX Knowledge Tests

_How well do male respondents understand government processes? These scenario-based questions reveal mental models._

### Who Issues the NIC?

```sql male_nic_knowledge
SELECT
    `Scenario: You lost your wallet and need a new ID. Who issues the National Identity Card?` as answer,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Male'
    AND `Scenario: You lost your wallet and need a new ID. Who issues the National Identity Card?` IS NOT NULL
GROUP BY answer
ORDER BY responses DESC
```

{% bar_chart
    data="male_nic_knowledge"
    x="answer"
    y="responses"
    title="Who Issues the NIC? — Male Respondents"
    subtitle="93.7% correctly identified DRP. 4% said 'I have no idea'"
    y_fmt="num0"
/%}

### How Would You Find Revenue License Renewal?

```sql male_search_method
SELECT
    CASE
        WHEN lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%revenue license%' OR lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%revenue licence%' THEN 'Search: Revenue License'
        WHEN lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%department of motor%' OR lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%dmt%' OR lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%department of transport%' THEN 'Search: DMT / Department'
        WHEN lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%income permit%' THEN 'Search: Income Permit'
        WHEN lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%vehicle service%' OR lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%drive%' THEN 'Browse: Vehicle Services / Drive'
        WHEN lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%don%t know%' OR lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%no idea%' THEN 'I Don''t Know'
        ELSE 'Other'
    END as search_approach,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Male'
    AND `You need to renew your car Revenue License. How would you look for it in the app?` IS NOT NULL
GROUP BY search_approach
ORDER BY responses DESC
```

{% bar_chart
    data="male_search_method"
    x="search_approach"
    y="responses"
    title="How Men Would Search for Revenue License Renewal"
    subtitle="Search vs Browse — reveals mental models for app navigation"
    y_fmt="num0"
/%}

# 👩 Female Respondents — Full Survey Analysis

_A comprehensive look at what **369 female respondents** (18.4% of the survey) said across all survey questions._

```sql female_summary
SELECT
    count(*) as total_female,
    round(count(*) * 100.0 / (SELECT count(*) FROM standardized_survey_data_2000_standardized_survey_data_2000), 1) as pct_of_total,
    round(avg(`How do you feel about the government having all your data (health, vehicle, ID) in one app?`), 2) as avg_data_comfort,
    round(avg(`What do you think about the Gov Pay feature in this app?`), 2) as avg_govpay_rating,
    round(avg(`What is the mobile signal strength in your area?`), 2) as avg_signal_strength
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Female'
```

{% big_value
    data="female_summary"
    value="total_female"
    title="Female Respondents"
    fmt="num0"
/%}

{% big_value
    data="female_summary"
    value="avg_data_comfort"
    title="Avg Data Comfort (1-5)"
    fmt="num1"
/%}

{% big_value
    data="female_summary"
    value="avg_govpay_rating"
    title="Avg GovPay Rating (1-5)"
    fmt="num1"
/%}

{% big_value
    data="female_summary"
    value="avg_signal_strength"
    title="Avg Signal Strength (1-5)"
    fmt="num1"
/%}

## Demographics

```sql female_by_district
SELECT
    `If you are in Sri Lanka, where do you live?` as district,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Female'
    AND `If you are in Sri Lanka, where do you live?` IS NOT NULL
GROUP BY district
ORDER BY responses DESC
```

{% bar_chart
    data="female_by_district"
    x="district"
    y="responses"
    title="Female Respondents by District"
    y_fmt="num0"
    x_axis_options={label_rotate=45}
/%}

```sql female_by_education
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
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Female'
GROUP BY education_level
ORDER BY responses DESC
```

{% bar_chart
    data="female_by_education"
    x="education_level"
    y="responses"
    title="Female Respondents by Education Level"
    y_fmt="num0"
/%}

```sql female_by_phone
SELECT
    `What phone do you use?` as phone_type,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Female'
    AND `What phone do you use?` IS NOT NULL
GROUP BY phone_type
ORDER BY responses DESC
```

{% bar_chart
    data="female_by_phone"
    x="phone_type"
    y="responses"
    title="Female Respondents by Phone Type"
    y_fmt="num0"
/%}

## What Frustrates Women Most?

```sql female_frustrations
SELECT
    CASE
        WHEN lower(f) LIKE '%queue%' OR lower(f) LIKE '%wasting time%' OR lower(f) LIKE '%long time%' OR lower(f) LIKE '%waiting%' THEN 'Queues / Long waits'
        WHEN lower(f) LIKE '%multiple counter%' OR lower(f) LIKE '%multiple office%' OR lower(f) LIKE '%multiple building%' OR lower(f) LIKE '%many place%' THEN 'Multiple counters/offices'
        WHEN lower(f) LIKE '%status%' OR lower(f) LIKE '%ready%' OR lower(f) LIKE '%still have to wait%' OR lower(f) LIKE '%tracking%' THEN 'Not knowing status'
        WHEN lower(f) LIKE '%rude%' OR lower(f) LIKE '%unhelpful%' OR lower(f) LIKE '%harsh%' OR lower(f) LIKE '%attitude%' THEN 'Rude / unhelpful staff'
        WHEN lower(f) LIKE '%where to go%' OR lower(f) LIKE '%confusion%' OR lower(f) LIKE '%exact location%' OR lower(f) LIKE '%confused%' OR lower(f) LIKE '%unclear%' THEN 'Not knowing where to go'
        WHEN lower(f) LIKE '%corrupt%' OR lower(f) LIKE '%bribe%' THEN 'Corruption / Bribes'
        WHEN lower(f) LIKE '%online%' OR lower(f) LIKE '%digital%' OR lower(f) LIKE '%website%' THEN 'Lack of digital services'
        WHEN lower(f) LIKE '%paper%' OR lower(f) LIKE '%document%' OR lower(f) LIKE '%form%' THEN 'Paperwork / Documents'
        ELSE NULL
    END as frustration_theme,
    count(*) as mentions
FROM standardized_survey_data_2000_standardized_survey_data_2000
ARRAY JOIN
    arrayFilter(x -> x != '',
        arrayMap(x -> trim(x),
            splitByString(',', assumeNotNull(`What is the single most frustrating part of dealing with the government?`))
        )
    ) as f
WHERE `What is your gender?` = 'Female'
    AND `What is the single most frustrating part of dealing with the government?` IS NOT NULL
    AND frustration_theme IS NOT NULL
GROUP BY frustration_theme
ORDER BY mentions DESC
```

{% horizontal_bar_chart
    data="female_frustrations"
    y="frustration_theme"
    x="mentions"
    title="Top Frustrations — Female Respondents"
    subtitle="What is the single most frustrating part of dealing with the government?"
    y_sort="data"
    x_fmt="num0"
/%}

## What Services Do Women Want?

```sql female_expected_services
SELECT
    CASE
        WHEN lower(s) LIKE '%vehicle%' OR lower(s) LIKE '%license%' OR lower(s) LIKE '%licence%' OR lower(s) LIKE '%driving%' THEN 'Vehicle & Driving License'
        WHEN lower(s) LIKE '%hospital%' OR lower(s) LIKE '%health%' OR lower(s) LIKE '%medical%' OR lower(s) LIKE '%clinic%' THEN 'Healthcare'
        WHEN lower(s) LIKE '%police%' OR lower(s) LIKE '%clearance%' OR lower(s) LIKE '%traffic%' OR lower(s) LIKE '%fine%' THEN 'Police & Traffic'
        WHEN lower(s) LIKE '%birth%' OR lower(s) LIKE '%death%' OR lower(s) LIKE '%marriage%' OR lower(s) LIKE '%certificate%' THEN 'Civil Registration'
        WHEN lower(s) LIKE '%passport%' OR lower(s) LIKE '%immigration%' THEN 'Passport & Immigration'
        WHEN lower(s) LIKE '%nic%' OR lower(s) LIKE '%identity%' OR lower(s) LIKE '%id card%' THEN 'National ID (NIC)'
        WHEN lower(s) LIKE '%tax%' OR lower(s) LIKE '%revenue%' OR lower(s) LIKE '%epf%' THEN 'Tax & Revenue'
        WHEN lower(s) LIKE '%education%' OR lower(s) LIKE '%school%' OR lower(s) LIKE '%exam%' THEN 'Education'
        WHEN lower(s) LIKE '%transport%' OR lower(s) LIKE '%bus%' OR lower(s) LIKE '%train%' THEN 'Public Transport'
        WHEN lower(s) LIKE '%water%' OR lower(s) LIKE '%electric%' OR lower(s) LIKE '%utility%' OR lower(s) LIKE '%bill%' THEN 'Utility Payments'
        WHEN lower(s) LIKE '%land%' OR lower(s) LIKE '%property%' OR lower(s) LIKE '%deed%' THEN 'Land & Property'
        WHEN lower(s) LIKE '%grama%' OR lower(s) LIKE '%divisional%' OR lower(s) LIKE '%secretariat%' OR lower(s) LIKE '%niladhari%' THEN 'Grama Niladhari / Divisional'
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
WHERE `What is your gender?` = 'Female'
    AND `What are the government services you would expect to be on the app?` IS NOT NULL
    AND service_category IS NOT NULL
GROUP BY service_category
ORDER BY mentions DESC
```

{% horizontal_bar_chart
    data="female_expected_services"
    y="service_category"
    x="mentions"
    title="Expected Government Services — Female Respondents"
    subtitle="What services do women want on the app?"
    y_sort="data"
    x_fmt="num0"
/%}

## If You Could Digitize ONE Thing...

```sql female_digitize_one
SELECT
    CASE
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%nic%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%identity%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%id card%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%national id%' THEN 'National ID (NIC)'
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%license%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%licence%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%driving%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%vehicle%' THEN 'Vehicle & Driving License'
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%hospital%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%health%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%medical%' THEN 'Healthcare'
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%birth%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%death%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%marriage%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%certificate%' THEN 'Civil Registration'
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%police%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%clearance%' THEN 'Police Clearance'
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%passport%' THEN 'Passport'
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%tax%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%revenue%' THEN 'Tax & Revenue'
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%land%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%property%' THEN 'Land & Property'
        WHEN lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%grama%' OR lower(`If you could digitize ONE thing immediately, what would it be?`) LIKE '%divisional%' THEN 'Grama Niladhari'
        ELSE NULL
    END as digitize_category,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Female'
    AND `If you could digitize ONE thing immediately, what would it be?` IS NOT NULL
    AND digitize_category IS NOT NULL
GROUP BY digitize_category
ORDER BY responses DESC
```

{% horizontal_bar_chart
    data="female_digitize_one"
    y="digitize_category"
    x="responses"
    title="If You Could Digitize ONE Thing — Female Respondents"
    subtitle="The single most urgent digitization priority"
    y_sort="data"
    x_fmt="num0"
/%}

## App Preferences

### Login Method

```sql female_login
SELECT
    CASE
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%biometric%' OR lower(`How would you prefer to log in to this app?`) LIKE '%face%' OR lower(`How would you prefer to log in to this app?`) LIKE '%fingerprint%' THEN 'Biometrics (Face/Fingerprint)'
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%otp%' OR lower(`How would you prefer to log in to this app?`) LIKE '%sms%' THEN 'OTP (SMS)'
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%username%' OR lower(`How would you prefer to log in to this app?`) LIKE '%password%' THEN 'Username & Password'
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%scan%' OR lower(`How would you prefer to log in to this app?`) LIKE '%barcode%' OR lower(`How would you prefer to log in to this app?`) LIKE '%physical id%' THEN 'Physical ID Scan'
        WHEN lower(`How would you prefer to log in to this app?`) LIKE '%mfa%' OR lower(`How would you prefer to log in to this app?`) LIKE '%multi%' OR lower(`How would you prefer to log in to this app?`) LIKE '%two factor%' OR lower(`How would you prefer to log in to this app?`) LIKE '%2fa%' THEN 'Multi-Factor Auth'
        ELSE 'Other'
    END as login_method,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Female'
    AND `How would you prefer to log in to this app?` IS NOT NULL
GROUP BY login_method
ORDER BY responses DESC
```

{% bar_chart
    data="female_login"
    x="login_method"
    y="responses"
    title="Preferred Login Method — Female Respondents"
    y_fmt="num0"
/%}

### App Name Preference

```sql female_app_name
SELECT
    CASE
        WHEN lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%modern%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%onelanka%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%citizen+%' THEN 'Modern & Unified (OneLanka / Citizen+)'
        WHEN lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%official%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%govportal%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%descriptive%' THEN 'Official & Descriptive (GovPortal)'
        WHEN lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%traditional%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%heritage%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%serendip%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%janaseva%' THEN 'Traditional / Heritage'
        WHEN lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%short%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%catchy%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%+94%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%liolk%' THEN 'Short & Catchy (+94 / LioLK)'
        WHEN lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%fun%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%friendly%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%niyamai%' OR lower(`What kind of name sounds most "Trustworthy" for a Government App?`) LIKE '%saho%' THEN 'Fun / Friendly (Niyamai / Saho)'
        ELSE 'Other / Custom Suggestion'
    END as name_preference,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Female'
    AND `What kind of name sounds most "Trustworthy" for a Government App?` IS NOT NULL
GROUP BY name_preference
ORDER BY responses DESC
```

{% bar_chart
    data="female_app_name"
    x="name_preference"
    y="responses"
    title="Preferred App Name Style — Female Respondents"
    y_fmt="num0"
/%}

### Color Preference

```sql female_color
SELECT
    `Which color combination do you like the most?` as color_choice,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Female'
    AND `Which color combination do you like the most?` IS NOT NULL
GROUP BY color_choice
ORDER BY responses DESC
```

{% bar_chart
    data="female_color"
    x="color_choice"
    y="responses"
    title="Preferred Color Combination — Female Respondents"
    y_fmt="num0"
/%}

## Ratings & Sentiment

```sql female_ratings
SELECT
    'Data Comfort' as rating_type,
    `How do you feel about the government having all your data (health, vehicle, ID) in one app?` as score,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Female'
    AND `How do you feel about the government having all your data (health, vehicle, ID) in one app?` IS NOT NULL
GROUP BY rating_type, score
UNION ALL
SELECT
    'GovPay Feature' as rating_type,
    `What do you think about the Gov Pay feature in this app?` as score,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Female'
    AND `What do you think about the Gov Pay feature in this app?` IS NOT NULL
GROUP BY rating_type, score
UNION ALL
SELECT
    'Signal Strength' as rating_type,
    `What is the mobile signal strength in your area?` as score,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Female'
    AND `What is the mobile signal strength in your area?` IS NOT NULL
GROUP BY rating_type, score
ORDER BY rating_type, score
```

{% bar_chart
    data="female_ratings"
    x="score"
    y="responses"
    series="rating_type"
    title="Rating Distributions — Female Respondents (1-5 Scale)"
    subtitle="Data Comfort, GovPay Feature, and Signal Strength"
    y_fmt="num0"
/%}

## Desired Alerts & Reminders

```sql female_alerts
SELECT
    CASE
        WHEN lower(a) LIKE '%license%' OR lower(a) LIKE '%licence%' OR lower(a) LIKE '%vehicle%' OR lower(a) LIKE '%revenue%' THEN 'License/Vehicle Renewals'
        WHEN lower(a) LIKE '%status%' OR lower(a) LIKE '%update%' OR lower(a) LIKE '%progress%' THEN 'Application Status Updates'
        WHEN lower(a) LIKE '%payment%' OR lower(a) LIKE '%bill%' OR lower(a) LIKE '%due%' THEN 'Payment/Bill Reminders'
        WHEN lower(a) LIKE '%fine%' OR lower(a) LIKE '%traffic%' OR lower(a) LIKE '%violation%' THEN 'Fine/Traffic Alerts'
        WHEN lower(a) LIKE '%emergency%' OR lower(a) LIKE '%disaster%' OR lower(a) LIKE '%weather%' THEN 'Emergency/Disaster Alerts'
        WHEN lower(a) LIKE '%expir%' OR lower(a) LIKE '%renew%' THEN 'Document Expiry Alerts'
        ELSE NULL
    END as alert_category,
    count(*) as mentions
FROM standardized_survey_data_2000_standardized_survey_data_2000
ARRAY JOIN
    arrayFilter(x -> x != '',
        arrayMap(x -> trim(x),
            splitByString(',', assumeNotNull(`Which specific alerts and reminders would you like to receive through the App?`))
        )
    ) as a
WHERE `What is your gender?` = 'Female'
    AND `Which specific alerts and reminders would you like to receive through the App?` IS NOT NULL
    AND alert_category IS NOT NULL
GROUP BY alert_category
ORDER BY mentions DESC
```

{% horizontal_bar_chart
    data="female_alerts"
    y="alert_category"
    x="mentions"
    title="Desired Alerts & Reminders — Female Respondents"
    subtitle="What notifications do women want from the app?"
    y_sort="data"
    x_fmt="num0"
/%}

## UX Knowledge Tests

_How well do female respondents understand government processes?_

### Who Issues the NIC?

```sql female_nic_knowledge
SELECT
    `Scenario: You lost your wallet and need a new ID. Who issues the National Identity Card?` as answer,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Female'
    AND `Scenario: You lost your wallet and need a new ID. Who issues the National Identity Card?` IS NOT NULL
GROUP BY answer
ORDER BY responses DESC
```

{% bar_chart
    data="female_nic_knowledge"
    x="answer"
    y="responses"
    title="Who Issues the NIC? — Female Respondents"
    y_fmt="num0"
/%}

### How Would You Find Revenue License Renewal?

```sql female_search_method
SELECT
    CASE
        WHEN lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%revenue license%' OR lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%revenue licence%' THEN 'Search: Revenue License'
        WHEN lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%department of motor%' OR lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%dmt%' OR lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%department of transport%' THEN 'Search: DMT / Department'
        WHEN lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%income permit%' THEN 'Search: Income Permit'
        WHEN lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%vehicle service%' OR lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%drive%' THEN 'Browse: Vehicle Services / Drive'
        WHEN lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%don%t know%' OR lower(`You need to renew your car Revenue License. How would you look for it in the app?`) LIKE '%no idea%' THEN 'I Don''t Know'
        ELSE 'Other'
    END as search_approach,
    count(*) as responses
FROM standardized_survey_data_2000_standardized_survey_data_2000
WHERE `What is your gender?` = 'Female'
    AND `You need to renew your car Revenue License. How would you look for it in the app?` IS NOT NULL
GROUP BY search_approach
ORDER BY responses DESC
```

{% bar_chart
    data="female_search_method"
    x="search_approach"
    y="responses"
    title="How Women Would Search for Revenue License Renewal"
    subtitle="Search vs Browse — reveals mental models for app navigation"
    y_fmt="num0"
/%}