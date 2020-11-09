# peertube stress test 


### inputs

while we are doing a stress test of the entire peertube server, it would be interesting to also check what exactly the transcoding storage usage is. Since we have two methods of serving files, webtorrent and hls with p2p, we should start with lets say webtorrent enabled only and input a 1 GB mp4 with 1080p at 30fps. this should be the baseline for all input tests. then, we should have 5 output formats, 

* 1080p
* 720p
* 480p
* 0p

this test would tell us with a standard input and standard settings, how much does webtorrent enabled transcoded output take up space.

second test would be to disable webtorrent and enable hls with p2p and do the transcoding again.

third test would be to keep both enabled (i think that is possible) and see the storage usage.

s3 storage like minio should help in mitigating disk IO constraints. that needs to be investigated.

### rate limiting

if the server is under load, like the network connection is saturated or disk usage is 100% or the cpu usage is too high, does the software dynamically try to reduce the load or keeps on going until it fails?

1. i hypothesize peertube **should** disable direct downloads first, reducing the indirect load or at least try to restrict the speed.
2. i hypothesize peertube **should** stop transcoding jobs if heavy cpu usage detected and continue once load is reduced.
3. i hypothesize peertube **should** try to reduce users resolutions if heavy disk IO and network is detected, if less data is going through, more people can be served. graceful degradation is better than abrubtly stopping serving to someone
4. we need to see how much does server redundancy help in bandwidth because that is a big part of federation and we should be able to quantify this with two fresh servers.

## network tests

1. in the first test of how many people can be served by the server, an input of say 1080p 30fps should be imported and only 1080p be kept as the resolution. that would tell us how many concurrent users can be handled raw. this test would be done once with webtorrents and again with hls.

2.in a second test, same 1080p video would be tested but simultaneously 2-3 direct downloads would be initiated to test its impact on results of test 1.
3. test involving multiple resolutions, multiple clients and multiple downloads and transcoding jobs running at once to simulate a "real world" instance.
4. remote viewing from a third party instance.  if we have federation enabled, how much our bandwidth and disk IO is used or is it same as viewing from local instance?

## live streaming tests

taken from from https://github.com/Chocobozzz/PeerTube/pull/3250#issue-515433569
1. Real tests to adjust HLS playlist parameters (segments in the playlist, segments duration etc)
2. Adjust RTMP server params (timeout etc)
3. Real tests to know if just muxing works in most cases
4. Real tests to adjust live transcoding settings
5. (Angular bug?) When the player automatically plays a live, the *This live has not started yet. * message is not hidden
6. Live notifications for admins? (a live started etc)


