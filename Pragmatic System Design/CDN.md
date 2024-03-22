# CDN (Content Delivery Network)

#### Problem Addressed: 
   When servers located far from users(eg: server in US, but user in India) more latency will be a problem(US users may enjoy but not Indian users due to latency). Farther the distance request needs to travel more the hops it needs to have and more time it will travel
#### Expensive Solution:
Deploy more servers across the globe

Solution for the above problem is CDN

## CDN
- Keep your servers where there are(Eg: US). CDN have its own servers spread across the globe and put your static content on those servers so that users can fetch it from closest server.
- Basically a cache for static content
- Static Content: Images, CSS, HTML, JavaScript
- About static content, it should n't change too often

### Types
 - Push
   - You upload something to server, then it automatically updates across all CDN servers. So you initiate the process but then its automatic.
   - Good when not much static content(If million images not a good idea to use this)
- Pull
   - Lazy
   - If a user requests a content that is not in CDN, then only it fetches from server and cache it
   - For the first user, latency will be high but for later user it will be very low 

#### Summary
- Puts static content closest to users
- Reduces Costs(reducing servers across globe)
- Decreases latency
- Increases complexity  
