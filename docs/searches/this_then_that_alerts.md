# Detect Chain of Behaviors

To make a risk rule that looks for two rules firing close together, we can use `sort` followed by the `autoregress` command within a certain duration:

```shell linenums="1"
index=risk sourcetype=stash search_name="Search1" OR search_name="Search2"
| sort by user _time | dedup _time search_name user
| delta _time as gap
| autoregress search_name as prev_search
| autoregress user as prev_user
| where user = prev_user
| table _time gap src user prev_user search_name prev_search
| where ((search_name="Search1" OR search_name="Search2") AND (prev_search="Search1" OR prev_search="Search2") AND gap<600)
```

The benefit of not doing this in a single search is you still have the individual risk events as useful observations, and then can add more risk when observed together, or tweak risk down for noisy events without "allowlisting" altogether.

---
<small>Authors</small>

<div class="zts-tooltip">
    <a class="zts-author" href="../../contributing/contributors" target="_blank" alt="7thdrxn - Haylee Mills">
        <img class="github-avatar" src="https://avatars.githubusercontent.com/u/12771156?v=4){ class="github-avatar"/>
    </a>
    <span class="zts-tooltip-text">@7thdrxn - Haylee Mills</span>
</div>