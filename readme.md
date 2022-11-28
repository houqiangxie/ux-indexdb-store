<!--
 * @Descripttion: 
 * @version: 
 * @Author: houqiangxie
 * @Date: 2022-10-09 16:42:35
 * @LastEditors: houqiangxie
 * @LastEditTime: 2022-11-28 15:40:38
-->
#usage


##set

import { set } from 'idb-keyval';

set('hello', 'world',5000);



##get
import { get } from 'idb-keyval';

// logs: "world"
await  get('hello')


##setMany

import { set, setMany } from 'idb-keyval';

setMany([
  [123, 456],
  ['hello', 'world'],
])


##getMany 
import { get, getMany } from 'idb-keyval';

// It's faster to do:
getMany([123, 'hello']).then(([firstVal, secondVal]) =>
  console.log(firstVal, secondVal),
);


##update:
Transforming a value (eg incrementing a number) using get and set is risky, as both get and set are async and non-atomic:

// Don't do this:
import { get, set } from 'idb-keyval';

get('counter').then((val) =>
  set('counter', (val || 0) + 1);
);

get('counter').then((val) =>
  set('counter', (val || 0) + 1);
);
With the above, both get operations will complete first, each returning undefined, then each set operation will be setting 1. You could fix the above by queuing the second get on the first set, but that isn't always feasible across multiple pieces of code. Instead:

// Instead:
import { update } from 'idb-keyval';

update('counter', (val) => (val || 0) + 1);
update('counter', (val) => (val || 0) + 1);
This will queue the updates automatically, so the first update set the counter to 1, and the second update sets it to 2.

##del:
Delete a particular key from the store.

import { del } from 'idb-keyval';

del('hello');
delMany:
Delete many keys at once. This is faster than calling del multiple times.

import { del, delMany } from 'idb-keyval';

// Instead of:
Promise.all([del(123), del('hello')])
  .then(() => console.log('It worked!'))
  .catch((err) => console.log('It failed!', err));

// It's faster to do:
delMany([123, 'hello'])
  .then(() => console.log('It worked!'))
  .catch((err) => console.log('It failed!', err));
##clear:
Clear all values in the store.

import { clear } from 'idb-keyval';

clear();
##entries:
Get all entries in the store. Each entry is an array of [key, value].

import { entries } from 'idb-keyval';

// logs: [[123, 456], ['hello', 'world']]
entries().then((entries) => console.log(entries));
keys:
Get all keys in the store.

import { keys } from 'idb-keyval';

// logs: [123, 'hello']
keys().then((keys) => console.log(keys));
values:
Get all values in the store.

import { values } from 'idb-keyval';

// logs: [456, 'world']
values().then((values) => console.log(values));
##Custom stores:
By default, the methods above use an IndexedDB database named keyval-store and an object store named keyval. If you want to use something different, see custom stores.

