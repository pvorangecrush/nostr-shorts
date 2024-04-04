# nostr-shorts

### Design
* Website:
  * Simple interface:
    * Upload video
    * Add a message (limit to 100 characters)
    * npub
  * Ad-supported interface
    * Choose one of the following ads to tag your message
      * Ad 1 "Add text / link" + XX minutes (e.g. 360) free hosting minutes
      * Ad 2 "Add text / link" + XX minutes (e.g. 360) free hosting minutes
      * Ad 3 "Add text / link" + XX minutes (e.g. 360) free hosting minutes
      * Ad 4 "Add text / link" + XX minutes (e.g. 360) free hosting minutes
      * Pay out-of-pocket for extra hosting time. Currently XX (e.g. 100) sats/minute)
        * Function: enter minutes value: generate lightning invoice or (if more nostr-ish?) NWC event to sign  
* Video is processed:
  * Transcode to 720p
  * Trim to 30s
  * Create a link to video processed video
  * Delete original upload
* Nostr post is prepared
  * Kind-01
  * Construct Message
    * "Video Link"
    * "User message"
    * "nostr:npub..."
  * Limited nostr-client functionality:
    * Repost from your npub to your followers
    * ***creator**@nostr-shorts* - Creators can post to their own feed
* Kind-01 is posted from:
  * Main catch-all video account: *_@nostr-shorts* 
  * Subscriber *creator@nostr-shorts* account
  * Curated tag accounts:
    * *trending@nostr-shorts*
    * *sports@nostr-shorts*
    * *news@nostr-shorts*
    * *art@nostr-shorts*
    * *bitcoin@nostr-shorts*
    * Paid or invited: ***creator**@nostr-shorts*
  * Each note is posted by *_@nostr-shorts* and..
    * re-posted by curated tag accounts
    * zaps are tracked by original event_id
* Hosting consumes XX sats/minute; first XX (e.g. 360) minutes are free
  * Database:
    * npub = string 
    * upload_file_link = string
    * processed_file_link = string
    * event_id = int
    * requests = int
    * zaps = [{eventid: "", zap_amount: int}]
    * zap_sum = int
    * minutes_remaining = int
  * Database workers:
    * Update minutes_remaining
    * Distribute zap payouts according to rules:
      * User receives escalating returns:
      * traffic_cost = requests * video_size
      * storage_cost = video_size * cost_per_mb
      * costs = traffic_cost + storage_cost
      * margin = XX (e.g. 20%)
      * After hosting_fees + margin, user receives:
        * XX (e.g. 50%) of > 1,000
        * XX (e.g. 75%) of > 10,000
        * XX (e.g. 90%) of > 25,000
        * XX (e.g. 99%) of > 100,000
        * XX (e.g. 99.9%) of > 1,000,000  
      * Payouts occur weekly as lightning zaps
        * Failed payments are held for XX (e.g. 30) days as balance to npub
        * Could avoid with partner fedi-mint or cashu P2PK mint like nostr.cash
