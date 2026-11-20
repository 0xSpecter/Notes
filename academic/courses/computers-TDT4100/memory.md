# Memory

[← Back: Computers TDT4100](./README.md)

## Volitile vs Non Volitile Memory

### RAM

Random access memory

## Memory Hierarchy

A memory hierarchy is a way of structuring memory to have
quick access times while still having a large storage capacity.

The key idea is that that smaller and faster memories are
closest to the CPU while slower but larger memories are farther away.
As signal travel distance is the primary time cost for the fastest memories,
while inconsequential for larger ones.

In most cases the memory is truly hierarchical, meaning that the
data cannot be present in a higher level without being present in the lower.

### Locality

The memory hierarchy takes advantage of locality by moving
recently accessed blocks closer to the prosessor.

#### Temporal locality

The locality principle stating that if data is referenced
then it will tend to be referenced again soon.

Examples:

1. When defining a variable in a function you often use it
more then once within it, in close succession.

2. Instructions and constants have high _temporal locality_ in loops.

> Page: 388-389

#### Spatial locality

The locality principle stating that if data is referenced,
data locations with nearby addresses will tend to be referenced soon.

Examples:

1. Like indexing an array, you load the first address then move on to
the next one which is just 1 offset away, then so on. This gives arrays
high _spatial locality_.

2. Data is stored like other types of information, for example books.
In a library book are catogorized into sections, so if your fetching
books about computer sciencs, and find the book about python, the book
concerning c++ is likely close by.

> Page: 388

### Speed v Size v Cost

The _fastest_ memory is also the smallest and most expensive per bit.
While the _slowest_ memory is the largest and least expensive per bit of memory.

The fastest memories are closest to the cpu, with _SRAM_, travel distance
of the signal accounts for between **20-35%** of the access time for L1 caches to **50-80%** for L3 caches.
With _DRAM_ it only accounts for between **0.5-2%**, While with HDD's
travel distance is essential 0% of the total access time.

#### Tech hierarchy

Hierarchy of memories with the fastest and most expensive first,
with the last being the slowest but least expensive per bit.

##### SRAM: Static Random Access Memory

Used for tiny caches on the prosessor.
Extreamly expensive per bit.

Access time: **0.5-2.5 ns**

Costs ~$500-1000 per GB, which is about 100,000x times more expensive
per bit then the cheapest memory, HDD's. Expensive cuz it used 6-10 transistors.

The amount in modern prosessors range from under 1 kilobyte in microcontrollers,
30-100 megabytes in gaming CPUs to 300+ megabytes in data center prosessors.

##### DRAM: Dynamic Random Access Memory

Used for both larger caches and the primary computer memory.

Access time: **50-70 ns**

Costs ~$15-20 per GB.

SDRAM: Synchronous dynamic RAM. Used a clock so the prosessor and
memory are always in sync.

DDR SDRAM: Double data rate Synchronous RAM. Still synced but also transfers
data on both the rising and falling edge of the clock, this
gives it twice the available bandwidth.

DDR4-3200 DRAM: 3200 means 3200 million transfers per second.
Since its double data rate its clocked at 1600-MHz.

##### Flash

Usally defined by SSD's, _solid-state-drives_, most laptops and modern pc's
use this over _hard drives_.

Access time: **5,000-50,000 ns**

The dominent form of persistent storage accross most modern devices,
esspecialy within portable devices.

Costs ~$0.15 per GB.

Flash drives are an implementation of
EEPROM: Electrically erasable programmable read only memory.

##### Magnetic Disk

Also known as HDD's, hard drives.

Access time: **5,000,000-20,000,000 ns**

The current champion for storing giant amounts of data that
are accessed infrequently. They suffer from being too slow at
data transfer for modern computing needs.

They also require more space per bit then _flash memory_,
making them unpractical for protable devices.

Primarily used in servers or by archivists.

Costs ~$0.010 per GB.

### Keywords

- Hit: The requested _block_ is in the cache.

- Miss: The requested _block_ is not in the cache.

- Block: the minimum unit of information that can be present or not present in a cache.

- Hit rate: The fraction of memory accesses found in a level of the memory hierarchy.
Equation: 1 - _miss rate_

- Miss rate: The fraction of memory accesses not found in a level of the memory hierarchy.
Equation: 1 - _hit rate_.

- Hit time: The time required to access a level of the memory hierarchy.
Including the time needed to determine whether the access is a hit or a miss.

- Miss penalty: The time required to fetch a block into a level of the memory hierarchy
from the lowest level, including the time to access the block, transmit it from one
level to the other, insert it in tthe level that experienced the miss, and then
pass the block to the requester.

## Caches

### Direct mapping

A cache structure in which each memory location is
mapped to exactly one location in the cache.

Mapping: (Block address) modulo (N blocks in cache)
