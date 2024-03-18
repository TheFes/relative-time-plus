[![hacs_badge](https://img.shields.io/badge/HACS-Default-orange.svg)](https://github.com/custom-components/hacs)
![Version](https://img.shields.io/github/v/release/TheFes/relative-time-plus)
[![Buy me a coffe](https://img.shields.io/static/v1.svg?label=%20&message=Buy%20me%20a%20coffee&color=6f4e37&logo=buy%20me%20a%20coffee&logoColor=white)](https://www.buymeacoffee.com/TheFes)
[![Paypal](https://img.shields.io/badge/PayPal-00457C?&color=00457c&logo=paypal&logoColor=white)](https://www.paypal.com/paypalme/thefes)

# Relative Time Plus

A jinja macro to display the difference between two datetimes in a readable format

## Why this macro

I hear you thinking, there is already a `relative_time` filter/function in Home Assistant. And of course this is true, but it only returns the text in English, and always only returns the biggest time fraction. So this macro supports multiple languages (currently Dutch and English) and has some additional options.

## How to install
You need to have Home Assistant 2023.4 or higher installed to use custom templates.
As of version 1.0 Home Assistant 2023.11 is required because it uses tests introduced in that version.

This custom template is compatible with [HACS](https://hacs.xyz/), which means that you can easily download and manage updates for it. Custom templates are currently only available in HACS when you enable experimental features. Make sure to enable it in the HACS settings, which you can access from Settings > [Devices & Services](https://my.home-assistant.io/create-link/?redirect=integrations) > HACS When experimental features are enabled you click the button below to add it to your HACS installation

[![Open your Home Assistant instance and open a repository inside the Home Assistant Community Store.](https://my.home-assistant.io/badges/hacs_repository.svg)](https://my.home-assistant.io/redirect/hacs_repository/?owner=TheFes&repository=relative-time-plus&category=template)


For a manual install you can copy the contents of `relative_time_plus.jinja` to a jinja file in your `custom_templates` folder.
Run the `homeassistant.reload_custom_templates` service call to load the file.

## Languages
Current supported languages:

|Language|Language code|Provided by|
|---|---|---|
|Danish|`dk`|[C4-Dimitri](https://github.com/C4-Dimitri)|
|Dutch|`nl`|[TheFes](https://github.com/TheFes)|
|English|`en`|[TheFes](https://github.com/TheFes)|
|French|`fr`|[Pulpyyyy](https://github.com/Pulpyyyy)|
|German|`de`|[fastlane086](https://github.com/fastlane086)|
|Italian|`it`|[SiriosDev](https://github.com/SiriosDev)|
|Polish|`pl`|[darylosu](https://github.com/darylosu)|
|Portuguese|`pt`|[FragMenthor](https://github.com/FragMenthor)|
|Swedish|`sv`|[tde01065](https://github.com/tde01065)|

## How to use
The only required field is the datetime you want to show as relative time. It can be eiter in the past or future, and you can use a datetime object, a timestamp (integer or float) or anything which can be converted to a datetime object using `as_datetime`.
Other optional fields are:

|name|type|default|example|description|
|---|---|---|---|---|
|`parts`|integer|`1`|`3`|The number of time fractions which should be used|
|`not_use`|list or string|`['millisecond']`|`['yr', 'minute']`|Provide the time periods you don't want to use in the output, by default only milliseconds are excluded. You can provide a list or a comma separated string, so `'yr, month'` and `['yr', 'monhth']` will both work. Possible values are `year`, `month`, `week`, `day`, `hour`, `minute`, `second`, `millisecond` or the abbreviated versions `yr`, `mth`, `wk`, `day`, `hr`, `min`, `sec`, `ms`.|
|`always_show`|list or string|`[]`|`['yr', 'mth', 'day']`|Provide the time periods you always want to show, even when they are zero. With the example the output could be `"0 years, 0 months and 19 days"`|
|`time`|boolean|`true`|`false`|Set to `false` to ignore time and only compare on date|
|`abbr`|boolean|`false`|`true`|Set to `true` to use the abbreviated phrases|
|`language`|string|`"en"`|`"nl"`|The country code (eg `dk` for Denmark) for the language to be used for the output|
|`compare_date`|datetime or timestamp|`now()`|`12345`|The datetime to compare the other datetime to|
|`round_mode`|string|`"floor"`|`"commmon"`|Determines how the last outputted value should be rounded. `"common"` will round up if the remainder is half or more, `"ceil"` will always round up if there is a remaining part, `"floor"` not take the remaining part into account|

Example usage:
Using a sensor state:
```jinja
{% from 'relative_time_plus.jinja' import relative_time_plus %}
{{ relative_time_plus(states('sensor.uptime'), parts=3, not_use=['week', 'millisecond'], time=true, abbr=true, language='nl') }}
```
This will output something like
`10 dg, 2 u en 7 min`

Using a last_changed datetime of an entity:
```jinja
{% from 'relative_time_plus.jinja' import relative_time_plus %}
{{ relative_time_plus(states.light.office.last_changed, 2) }}
```

This will output something like
`3 hours and 1 minute`

Using the `always_show` parameter (remove the `compare_date` parameter to use the current date):
```jinja
{% set current_date = '2023-12-25' %}
{{ relative_time_plus('2018-12-25', compare_date=current_date, not_use='wk', always_show=['yr', 'mth', 'day'], parts=3) }}
```

This will output
`5 years, 0 months and 0 days`


Using a date string:
```jinja
{% from 'relative_time_plus.jinja' import relative_time_plus %}
{{ relative_time_plus('2023-01-01', parts=2, time=false, not_use=['wk']) }}
```

This will output something like (assuming the current date is 9th of April 2023)
`3 months and 8 days`

## My language is not suported
You can either issue a PR with the language phrases, or create an issue with all the required phrases (so singular, plural and abbreviated per time section, a combine word and an error text) in an issue.
