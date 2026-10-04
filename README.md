# Kafka Backup

A 45-second Instagram reel for beginners on how Kafka keeps your data safe when a broker goes down. It has a hook image and five diagrams, one idea each.

It's part 2 of [Kafka Storage](../kafka-on-disk), which ends with: *"In the next reel, I'll explain how your data in Kafka survives even if a broker goes down."*

## The idea

Kafka never keeps just one copy of your data:

1. Every partition folder is **copied to 3 brokers**.
2. One copy is the **leader**. New events are written to it, and the **followers copy** them.
3. A broker can **die** at any time.
4. A follower becomes the **new leader**, with all the data already on its disk.
5. When the dead broker comes back, it **catches up** from the leader.

## The images

All in [images/](images/).

| # | Image | Size | Voiceover |
|---|---|---|---|
| Hook | [00-hook.png](images/00-hook.png) | 1080×1080, sits under the face cam | "What happens to your Kafka data if a broker dies?" |
| 1 | [01.png](images/01.png) | 1080×1920 | "Remember the orders-0 folder from the last reel? Kafka doesn't keep it on one machine. It keeps a copy of that folder on three different brokers." |
| 2 | [02.png](images/02.png) | 1080×1920 | "One copy is the leader. order-service always writes to the leader, and the other two, the followers, copy every new event from it." |
| 3 | [03.png](images/03.png) | 1080×1920 | "Now Broker 1, the leader, crashes." |
| 4 | [04.png](images/04.png) | 1080×1920 | "Kafka makes Broker 2 the new leader. It already has every event, so nothing is lost. order-service simply writes to Broker 2 now, and Broker 3 keeps copying." |
| 5 | [05.png](images/05.png) | 1080×1920 | "When Broker 1 comes back, it rejoins as a follower and copies whatever it missed. Three copies again." |
| Outro | face cam | | "So that's how Kafka survives a crash: three copies, one leader, and the followers always ready to take over." |

For a 20-second version, use the hook and images 2–4 only.

## Editing tips

- Images 01–05 keep their bottom line well above the bottom-right corner, so the face cam doesn't cover it.
- Use a slow push-in on each image.
- On image 1, reveal the Partition 0 box first, then the three arrows, then the three brokers.
- On image 2, pop the green **+** rows one after another: Broker 1 first, then Brokers 2 and 3 as the orange arrows land.
- On image 3, pop the red ✕ with a shake.
- Images 3 → 4 → 5 share the same layout, so a straight cut between them reads as one story: crash, takeover, recovery.

## Caption

> What happens to your Kafka data when a broker dies? Nothing 🛟
>
> 1️⃣ every partition is copied to 3 brokers
> 2️⃣ the leader takes writes, the followers copy them
> 3️⃣ a broker dies
> 4️⃣ a follower becomes the new leader, with nothing lost
> 5️⃣ the dead broker comes back and catches up
>
> #kafka #apachekafka #systemdesign #backend #softwareengineering #distributedsystems #dataengineering #programming

## If someone asks in the comments

- The number of copies is the **replication factor**. 3 is the usual choice. It's set per topic.
- The copies are called **replicas**. The followers that are fully caught up are the **ISR** (in-sync replicas). Only they can become the new leader, which is why nothing is lost.
- With `acks=all`, the producer is told "saved" only after every in-sync replica has the event.
- Kafka spreads the leaders out. Broker 1 might lead orders-0 while Broker 2 leads orders-1 and Broker 3 leads orders-2, so every broker shares the work.
- Consumers also read from the leader, so notification-service switches to Broker 2 along with order-service.
- Replication isn't a backup against mistakes. If you delete a topic or write bad data, the copies get the same change. For that, you need real backups or long retention.
- With 3 copies and `min.insync.replicas=2`, you can lose one broker and keep writing. Lose two and Kafka stops accepting writes rather than risk losing data.
