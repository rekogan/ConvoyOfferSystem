# Best driver for the job
When a shipper books a shipment with Convoy, we'll start offering the job to drivers one at a time. We need to find all the drivers that can carry the load, and then the best driver within that group. **We need you to write Version 1 of our offer system!**

We'll consider three factors, in priority order, when deciding the next driver to offer a given job.

### 1. Can the driver's truck carry the load?
For now, let's assume that each driver owns a single truck. Each driver/truck has a maximum capacity (weight), and each shipment as a required capacity. We shouldn't offer a job to a driver with a capacity lower than required.

### 2. Have we offered this driver the job before?
If they didn't notice the offer, or passed on it, we shouldn't waste our time or theirs by offering it to them again.

### 3. How many offers have they gotten?
We try to be fair to the drivers, and offer work to everyone equally. Between two drivers -- all other things being equal -- we should offer a job to the person who's had fewer chances for work.

# The interface
So that you aren't tied to a language or framework, your implementation should be a program that we can command through STDIN, and outputs to STDOUT. Each line of STDIN should be interpreted as a new command. Your program should be able to respond to the three types of events.

### Add new driver
```
DRIVER <capacity> <driver_name>
```
Other commands will refer to the driver by `<driver_name>`, and `<capacity>` will always be a positive integer. Your program should not respond with any output on STDOUT.

### Find driver for new shipment
```
SHIPMENT <shipment_id> <required_capacity>
```
Somebody just booked a new shipment! `<shipment_id>` will be some positive integer ID. Your program should emit the name of the best driver. Do that by printing in STDOUT the name of the driver best suited to carry this shipment. If there's no driver available, your program should emit `NOBODY` to indicate there's no one left to offer to. Printing a driver's name means we will immediately send them an offer.

### Process the outcome of an offer
```
OFFER <shipment_id> <result> <driver_name>
```
`<shipment_id>` and `<driver_name>` will be tokens from earlier, and `<result>` will be one of `(ACCEPT|PASS|EXPIRE)` indicating how the offer resolved. If the driver accepted the job, your program should not respond with any STDOUT. Otherwise, your program should emit the name of the best driver, similarly to the `SHIPMENT` command.

### Examples of the interface
So that both you (and we!) can copy-paste our examples: your interpreter should ignore all characters after `#` on a line.

**Single driver, with an ACCEPT**

Input:
```bash
DRIVER 42 alice            # 1. Add a driver with a capacity of 42
SHIPMENT 1 40              # 2. Register a shipment requiring capacity of 40
OFFER 1 ACCEPT alice       # 3. Alice accepted the job. Success!
```
Output:
```bash
alice                      # from (2): Alice is the only driver in our system and she has capacity for this shipment
                           # from (3): Since Alice accepted the job, we didn't output anything
```

**Repeated searches**

Input:
```bash
DRIVER 42 alice            # 1. Add a driver with a capacity of 42
SHIPMENT 1 40              # 2. Shipment 1 requires a capacity of 40
DRIVER bob 50              # 3. Add a driver
DRIVER carol 20            # 4. Add a driver
SHIPMENT 2 40              # 5. Shipment 2 also required capacity of 40
OFFER 1 PASS alice         # 6. Shipment 1 was passed-on by alice
OFFER 1 ACCEPT bob         # 7. Shipment 1 was accepted by bob
```
Output:
```bash
alice    # from (2) Alice is the only driver in our system and she has capacity for this shipment
bob      # from (5) Bob and Alice can both accept Shipment 2, but Alice already had an offer today. Bob hasn't yet.
bob      # from (6) Bob is the only driver who can accept shipment 1, and Alice has already passed.
         # from (7) No output required
```

**A shipment with no drivers**

Input:
```bash
DRIVER 10 carol    # 1. Add a driver
SHIPMENT 2 15      # 2. There's only one driver, and they don't have the capacity for this shipment
```
Output:
```bash
NOBODY             # from (2) We don't have a driver with a capacity of 15
```

# The code
You can solve this using any language you like; please make it as professional as you would for production. We're big believers in testing, so include any tests you've written, as well as directions on how to build and run your code.

