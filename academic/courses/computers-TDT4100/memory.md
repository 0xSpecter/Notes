# Memory

[← Back: Computers TDT4100](./README.md)

## Volatile vs Non-Volatile Memory

### RAM

Random access memory

## Memory Hierarchy

A memory hierarchy is a way of structuring memory to have
quick access times while still having a large storage capacity.

The key idea is that smaller and faster memories are
closest to the CPU while slower but larger memories are farther away.
As signal travel distance is the primary time cost for the fastest memories,
while inconsequential for larger ones.

In most cases the memory is truly hierarchical, meaning that the
data cannot be present in a higher level without being present in the lower.

### Locality

The memory hierarchy takes advantage of locality by moving
recently accessed blocks closer to the processor.

#### Temporal locality

The locality principle stating that if data is referenced
then it will tend to be referenced again soon.

Examples:

1. When defining a variable in a function you often use it
more than once within it, in close succession.

2. Instructions and constants have high _temporal locality_ in loops.

> Page: 388-389

#### Spatial locality

The locality principle stating that if data is referenced,
data locations with nearby addresses will tend to be referenced soon.

Examples:

1. Like indexing an array, you load the first address then move on to
the next one which is just 1 offset away, and so on. This gives arrays
high _spatial locality_.

2. Data is stored like other types of information, for example books.
In a library books are categorized into sections, so if you're fetching
books about computer science, and find the book about python, the book
concerning c++ is likely close by.

> Page: 388

### Speed v Size v Cost

The _fastest_ memory is also the smallest and most expensive per bit.
While the _slowest_ memory is the largest and least expensive per bit of memory.

The fastest memories are closest to the cpu, with _SRAM_, travel distance
of the signal accounts for between **20-35%** of the access time for L1 caches to **50-80%** for L3 caches.
With _DRAM_ it only accounts for between **0.5-2%**, While with HDD's
travel distance is essentially 0% of the total access time.

#### Tech hierarchy

Hierarchy of memories with the fastest and most expensive first,
with the last being the slowest but least expensive per bit.

##### SRAM: Static Random Access Memory

Used for tiny caches on the processor.
Extremely expensive per bit.

Access time: **0.5-2.5 ns**

Costs ~$500-1000 per GB, which is about 100,000x times more expensive
per bit than the cheapest memory, HDD's. Expensive because it uses 6-10 transistors.

The amount in modern processors range from under 1 kilobyte in microcontrollers,
30-100 megabytes in gaming CPUs to 300+ megabytes in data center processors.

##### DRAM: Dynamic Random Access Memory

Used for both larger caches and the primary computer memory.

Access time: **50-70 ns**

Costs ~$15-20 per GB.

SDRAM: Synchronous dynamic RAM. Uses a clock so the processor and
memory are always in sync.

DDR SDRAM: Double data rate Synchronous RAM. Still synced but also transfers
data on both the rising and falling edge of the clock, this
gives it twice the available bandwidth.

DDR4-3200 DRAM: 3200 means 3200 million transfers per second.
Since its double data rate its clocked at 1600-MHz.

##### Flash

Usually defined by SSD's, _solid-state-drives_, most laptops and modern pc's
use this over _hard drives_.

Access time: **5,000-50,000 ns**

The dominant form of persistent storage across most modern devices,
especially within portable devices.

Costs ~$0.15 per GB.

Flash drives are an implementation of
EEPROM: Electrically erasable programmable read only memory.

##### Magnetic Disk

Also known as HDD's, hard drives.

Access time: **5,000,000-20,000,000 ns**

The current champion for storing giant amounts of data that
are accessed infrequently. They suffer from being too slow at
data transfer for modern computing needs.

They also require more space per bit than _flash memory_,
making them impractical for portable devices.

Primarily used in servers or by archivists.

Costs ~$0.010 per GB.

## Caches

### Keywords

- Hit: The requested _block_ is in the cache. Check is: _tag_ = _tag-array\[index\]_ AND _valid bit_ = 1

- Miss: The requested _block_ is not in the cache. When _hit_ = 0

- Block: the minimum unit of information that can be present or not present in a cache.

- Hit rate: The fraction of memory accesses found in a level of the memory hierarchy.
Equation: 1 - _miss rate_

- Miss rate: The fraction of memory accesses not found in a level of the memory hierarchy.
Equation: 1 - _hit rate_.

- Hit time: The time required to access a level of the memory hierarchy.
Including the time needed to determine whether the access is a hit or a miss.

- Miss penalty: The time required to fetch a block into a level of the memory hierarchy
from a lower level, including the time to access the block, transmit it from one
level to the other, insert it in the level that experienced the miss, and then
pass the block to the requester.

- **Byte offset**: Lowest part of the address, selects which byte in a word to load.

- **Block offset**: Next lowest part of the address that selects where in the block the data is.

- **Index**: Middle part of the address that selects the requested block in the cache.

- **Tag**: The upper part of the address used to identify whether the associated
block corresponds to the requested word. Usually stored in a tag-array the same length
as the cache and as wide as the tag.
Tag is the largest part of the address.

- **Valid bit**: A field in the tables of a memory hierarchy that indicates
that the associated block in the hierarchy contains valid data.

- **Cache misses**: A request for data from the cache that cannot be filled because the
data are not present in the cache.

- **Split cache**: One cache for data and one for instructions.

---

Writes:

- **Inconsistant cache**: When the value in the cache is diffrent from memory,
when their equal then their _consistent_.

- **Write-through**: Scheme where writes update both the cache and their next lower level
of memory, this ensures data is _consistent_.

- **Write-back**: Scheme where the data is only written to the block in the cache, then written
to the lower level when the the block is replaced.

- **Write buffer**: A queue that holds data while its waiting to be written to memory.

- **Write miss**: When the block written to is not present in the cache. Since you
don't overwrite blocks but write _into_ them, overwriting some or all of their content.

- **Write allocate**: Allocating a block in a cache.

- **No write-allocate**: Updating the block in memory but not putting the block
in the cache.

### Direct mapping

A cache structure in which each memory location is
mapped to exactly one location in the cache.

Mapping / indexing: (Block address) modulo (N blocks in cache)

in a 2^3 or 8 block direct mapped cache only the 3 lowest (rightmost) bits are used for addressing.
This gives you addresses from: _000_ to _111_.
So given the memory address _100110_ only _110_ is used to index the cache.

When 2 memory addresses correspond to the same cache address, they overwrite each other.
Leaving the most recent one in the cache. This is useful to take advantage of temporal locality.
So the most recently used data is closest to the processor. Spatial locality comes from
using multiword blocks, so loading 1010 also brings in the _close by_ address 1011.

Tagging is used to identify if the memory held in the cache is the correct data.

#### Calculate total bits in a cache

Size of block: words-per-block · word-size (in bits)
Size of offset (word): log2(words-per-block)
Size of byte offset: log2(bytes-per-word) (= 2 for 32-bit words)
Size of index: log2(n blocks) = log2(cache-words) - log2(words-per-block)
Size of tag: address-size - (index-size + offset-size + byte-offset-size)

Total bits: 2^index-size · (block-size + tag-size + 1 valid bit)

Given:

- 16 KiB of data.
- Four word blocks.
- 64bit addresses.

Find: Total bits.

16 KiB = 2^12 words (32-bit words).
address-size = 64 bits
block-size = 4 · 32 = 128
Size of offset = log2(4) = 2
Size of byte offset = log2(4) = 2
Size of index = 12 - 2 = 10.
Size of tag = 64 - (10 + 2 + 2) = 64 - 14 = 50

Total bits = 2^10 · (128 + 50 + 1) = 2^10 · 179 = 179 Kibibits = ~22.4 KiB

#### Mapping an address to a multiword block cache

Bytes per block: block-size / 8
Block address: floor(Byte-address / bytes-per-block)
Mapping address: block-address % n blocks (block-address still contains the tag, this removes it)

Given:

- 64 | 2^6 blocks.
- block size of 16 bytes | 4 words.

Find: What block number does the address 1200 (base 10) map to.

Block address = 1200 / 16 = 75
Mapping address: 75 % 64 = 11

### Set associative

...
