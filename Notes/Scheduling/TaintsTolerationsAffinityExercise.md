# Taints and tolerations and affinity exercies

- 5 nodes
    - 3 for our team (red, green, blue), 2 for rest of org
- 5 workloads
    - 3 for our team (red, green, blue), 2 for rest of our
- our 3 workloads should go into their respective nodes
- other 2 shouldn't run on our 3 nodes
- our workloads shouldn't run on other 2 nodes

# Solution
- taint our nodes 3 red, green and blue
    - no pods can be scheduled on them now
- add toleratinos to our 3 pods
    - only our pods can be placed on our nodes now :)
    - but our pods might be placed on other team's 2 nodes :(
- add red, green blue labels to our nodes
- add red, green, blue affinity to our pods
    - now our pods want to go onto our correct nodes :)