# Twitter User Sample
In 2013, I wrote an article called ["Tweets Loud and Quiet"](https://www.oreilly.com/ideas/tweets-loud-and-quiet) in which I argued that, although Twitter is sometimes characterized as a powerfully egalitarian, democratizing force, it actually resembles a traditional broadcast medium, with a handful of very influential celebrities on one side and many millions of people with just a couple of followers on the other.

In order to write that article, I scraped metadata for about 400,000 Twitter accounts. Since then, it's been cited in [a handful of scholarly papers](https://scholar.google.com/scholar?oi=bibs&hl=en&cites=509207419917937138&as_sdt=5), and I've gotten regular requests from researchers for access to my original dataset. I'm pleased to share my full dataset here.

This dataset was created between September 17, 2013, and October 19, 2013. Since I built it, Twitter has changed the way it assigns IDs, and a repeat of this project is now impossible (SEE BELOW SECTION LINK). Although this data might no longer provide a useful means of evaluating Twitter usage patterns, it's still interesting as a way to understand social dynamics.

## Background and collection
Until roughly 2015, Twitter gave every account a unique ID from a reasonably compact namespace. When I compiled this data in 2013, existing accounts had unique ID numbers that fell between roughly 0 and 1.9 billion.

I generated this dataset by polling Twitter's API for random account IDs within that range. It turns out that about 63% of integers between 0 and 1.9 billion were associated with accounts; Twitter's ID-assignment process skipped some numbers. Nevertheless, Twitter's ID assignment was strictly monotonic: given two accounts, the one with the higher ID number was always opened later than the other.

One other quirk: Twitter appears to have changed its account ID assignment algorithm around July 2012; before then, it assigned IDs at a density of 86%, and after then at 49%, skipping a large block of potential ID numbers in between.

Around 2015, Twitter switched from its quasi-sequential ID assignment scheme to one that resembles [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier). Now that Twitter accounts are assigned a 64-bit ID generated in part by hashing a timestamp, the namespace you'd need to search in order to repeat this project is *2<sup>64</sup> = 1.8 x 10<sup>19</sup>*, or roughly as many possible IDs as there are <a href="https://en.wikipedia.org/wiki/Orders_of_magnitude_(numbers)#1018">insects on earth</a>. I try to avoid writing things like this, but here I'm sure enough that it'd exceed the most fundamental constraints of modern computing: it can't be done.

## Fields
Here you'll find my Twitter sample as both a CSV file and an R dataset. The data in the files is identical.

| Field | Remarks |
| ----- | ------- |
| account_id | Unique account ID |
| handle | Twitter handle, e.g., [@JonBruner](https://twitter.com/jonbruner) |
| name | Full account name, e.g., "Jon Bruner" |
| description | Long account description, e.g., "Person who loves revelatory datasets that speak to eternal social characteristics." |
| url | URL associated with account description |
| language | [ISO language code](https://www.loc.gov/standards/iso639-2/php/code_list.php) selected by user |
| location | Free-text location provided by user |
| account_created_at | Date and time of account creation |
| account_created_at_interpolated | See below |
| crawled_at | Date and time when account was scraped for inclusion in this dataset |
| missing | 0: account is not missing<br>1: account never existed with this ID<br>2: account closed (by user, or by Twitter for abuse) |
| protected | 0: not protected<br>1: protected |
| followers_count | Number of Twitter accounts following this account |
| following_count | Number of Twitter accounts that this account follows |
| statuses_count | Number of public posts created by this account |
| listed_count | Number of lists on which this account appears |
| last_post_id | Unique ID of this account's most recent post, as of `crawled_at` |
| last_post_text | Text of this account's most recent post |
| last_post_lat | Latitude, if available, for this account's most recent post |
| last_post_lon | Longitude, if available, for this account's most recent post |
| last_post_place_id | [Twitter place ID](https://dev.twitter.com/overview/api/places), if available, for this account's most recent post |
| last_post_created_at | Date and time of this account's most recent post |
| time_since_last_post | Convenience variable: the difference in hours between `crawled_at` and `last_post_created_at` |

### Interpolated account creation time
For any `account_id` that I polled, there could be one of three results:
1. An account associated with the `account_id` exists
2. An account associated with the `account_id` existed at one time, but was closed, either voluntarily by the owner or (more commonly, I imagine) by Twitter for abuse
3. The `account_id` has never been assigned to an account

In case 1, I was able to retrieve an `account_created_at` value. In case 3, the creation datetime is moot. In case 2, it might be interesting to know when the now-closed account was created, but Twitter's API doesn't return a creation datetime. So, I interpolated a creation datetime for each missing account on the assumption that Twitter's ID assignment scheme was roughly linear over small intervals. So, you'll see something like this:

| account_id | account_created_at | account_created_at_interpolated |
| ---------- | ------------------ | ------------------------------- |
| 60453 | 2006-12-12 13:25:33 | 2006-12-12 13:25:33 |
| 63729 | - | 2006-12-13 11:39:21 |
| 72373 | 2006-12-15 22:18:44 | 2006-12-15 22:18:44 |

## Legal
[Twitter's developer terms](https://dev.twitter.com/overview/terms/agreement-and-policy) now prohibit the use of its API "to monitor or measure the...usage statistics or results of Twitter Services," including for the compilation of "aggregate Twitter user metrics such as total number of active users, accounts, user engagements, or account engagements." However, they [did not include this prohibition when I built the user dataset in 2013](https://web-beta.archive.org/web/20131203053715/https://dev.twitter.com/terms/api-terms).

You may use this dataset for any purpose. If you do so, kindly cite Jon Bruner as the author of the data.
