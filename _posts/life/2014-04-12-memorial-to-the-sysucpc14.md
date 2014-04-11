---
layout: post
title: "My final CPC: memorial to SYSUCPC 14'"
category: life
description: There're two contests this year. Almost 150 teams took part in the first one and the top 40 teams have the right to take the second one. Though we had a good score in the first one (ranking 16), we still failed to keep it in the second contest.
---

## Our team

My teammates are [txx][1] and [Ghost][2]. Actually, we teamed up when we were freshmen to participate in the SYSUCPC 11'. But at that time we were kind of arrogant because three of us started programming before entering university and they two even didn't take the GaoKao (winning the first prize in the NOIP will grant you an admission to almost any university in China). The arrogance kicked us and we didn't win the first prize and hence the entrance to the GDCPC 11'. 

If you read the About Me page carefully, you will know that txx and Ghost are also my team members in Martin Network. Indeed, we are an effective team.

## Why we participate in SYSUCPC in our senior year?

During the registration, we can hardly see teams consist of senior students. Many senior students are engaged in the internship or travelling. But three of us are relatively free this time. Txx is now a technical director in an steadily-developing start-up. Ghost left Flamingo and is now looking for a new employer. As for me, I'm living the most comfortable life among classmates after I accepted the admission from CMU. 

Besides, we think this is the last time we can participate in a CPC. So we don't want to waste the chance to memorize the time when we were fighting in an programming contest. All of us started participating in such contest at least in ninth grade. So it means much in our lives. 

## Online preliminary contest

The number of teams registered for SYSUCPC 14' exceeded 150, the maximun capacity of the computer room. So there has to be a preliminary contest to select 150 teams to take the formal SYSUCPC 14'. This contest was held at [Sicily][3], the online judge system of SYSU. 

At that night of the contest, Ghost was sick and given a drip in hospital. Txx was still in Beijing, meeting clients of the start-up. So I had to solve the problems on my own. Haven't coded for about one year, I was a little bit nervous. But several years' programming experience made me solve the first two easy problems quickly. After gaining some confidence, I started to solve the third problem. I knew how to solve, but it was just a little tricky. I coded for about an hour and finally it was accepted after two submissions.

Realizing that not much time left and we were at a good ranking, I stopped writing and started to chat with txx and Ghost. They were discussing how another problem could possibly be solved. And I occasionally expressed my opinions and joked. Anyway, this contest is more a warm-up than a contest for us.

## First round contest

Not like before, SYSUCPC 14' consists of two separate contests. Top 40 teams will be authorized to take the second one. The second contest is Guangdong VS Zhejiang CPC. There're ten teams from ten universities respectively from Guangdong and Zhejiang, five from GD and five from ZJ. Other selected teams would take the synchronous contest, which means we solve the problems at the same time with those 10 teams, but we share different rankings. 

So the goal of the first contest would be ranking top 40. At first we were not confident about that because three of us hadn't trained for more than one year, but after careful analysis, we thought we have great chance. And it turned out to be right.

In the first hour we solved three easy problems. After that, txx was coding on a simple but tricky problem. And I was working on a pure dynamic programming problem. but with extremely large scale input data (10^9). An algorithm with O(n) time complexity would result in TLE. So we had to either think of a mathematical method to simplify it, or reduce the complexity to O(lgn). 

When I was struggling, txx's program was accepted and only one hour was left. At that time, we ranked 30+ and was at the fringe entering second round. Before that, I was trying mathematical methods, but failed. So I turned to state transition function. After several tries, I successfully divided the sub-problem in a half scale. So the overall complexity became O(lgn). I was so excited and went to code.

The first time I submitted my code, I got WA. Would that be caused by a wrong state transition function? I checked the function carefully and assured the righteousness of it. Curious enough, I read my code one line by another. And I found that I wrote "a * b;" instead of "a *= b;" by mistake. What an idiot. After correcting this, I submitted again and got Accepted. At that time only 20 minutes left.

Finally in the first round, we ranked 16, which surprised us a lot. And we had a great dinner in an Mexican Restaurant. BTW, there are almost all foreigners in that restaurant. Txx and Ghost said the food was delicious, but I tasted bland because I got a cold that day. After the dinner I felt even worse. I went back to dormitory quickly, took a shower and went to sleep early.

## Second round contest

Thanks to the antibiotic, I was feeling well at the day of second round contest. With the good ranking in the first round, we were confident about ourselves. 

Five minutes after the contest began, more and more teams solved the first problem. It was a game theory problem and we couldn't find a way to solve it. With simple test, we guessed that if the size is larger than 3, the first player wins, otherwise the second. Apparently it was wrong. But almost all teams solved that problem, we thought it must be very simple. So we guessed that when the size is even, the first player wins, otherwise the second. Surprisingly but not unexpectedly, it was accepted. So we quickly moved on to the next problem.

The second is a greedy algorithm. After several discussions, I began coding. The first submission got TLE because I set a three-dimensional array with a 3-level nested loop rather than memset() function. After I using memset(), it was accepted. So far so good.

However, our tragedy began. Txx and I was think about a mathematical problem, which many teams solved, while Ghost was coding on a simple but very tricky problem. Three hours passed, and neither of us made it through. It seemed that the mathematical problem could be solved, but the idea slipped away every time I tried to catch it. Ghost always had a bad reputation on his correctness of coding. This time was no exception. WA, WA and WA. Finally we only solved two problems. 

After the contest, we went to MingChu in Jiangnan Xi for dinner. The curry there catches me every time. At dinner, we talked about the Sui Bian in RBT. Ghost recommended it but both txx and I hadn't tasted. So we gave it a try after the dinner. Still not satisfied, we went to Mr. X to play Penetralium Escaping. Maybe we were disappointed about our performance in the contest, we played hard to escape from penetralium. In the end, we managed to escape right before the time ran out. Not a bad day ha.

<img src="/images/memorial_to_sysucpc14/p1.jpg">


## Prostscript

Txx said after the second round contest, 'never let Ghost debug more than 1 hour next time'. Aha indeed. But this is the last time. Where is next time?

[1]: http://blog.t-xx.me/
[2]: http://www.ghost233.me/
[3]: http://soj.me/