# MuntahaCache Module Documentation

The `MuntahaCache` class module provides a comprehensive caching solution for web applications. This includes options for caching in various storage types (`local`, `session`, or `Cache Storage`) with automatic expiration, Least Recently Used (LRU) eviction strategies, and optional auto-caching based on access frequency.

---

## Table of Contents

- [Types and Interfaces](#types-and-interfaces)
  - [CacheKey](#cachekey)
  - [CacheOptions](#cacheoptions)
  - [CachedEntry](#cachedentry)
- [Constants](#constants)
  - [CACHE_NAME](#cache_name)
  - [DEFAULT_TTL](#default_ttl)
  - [MAX_CACHE_SIZE](#max_cache_size)
- [MuntahaCache Class](#MuntahaCache-class)
  - [Properties](#properties)
  - [Methods](#methods)
    - [getLocal](#getlocal)
    - [getSession](#getsession)
    - [getCache](#getcache)
    - [get](#get)
    - [setLocal](#setlocal)
    - [setSession](#setsession)
    - [setCache](#setcache)
    - [set](#set)
    - [delete](#delete)
    - [clearAll](#clearall)
    - [applyLRU](#applylru)
    - [convertImageToBlob](#convertimagetoblob)
    - [setMedia](#setmedia)
    - [getMedia](#getmedia)

---

## Types and Interfaces

### `CacheKey`

- **Type**: `string`
- **Description**: Represents the unique identifier for each cached entry.

### `CacheOptions`

- **Fields**:
  - `ttl?: number`: Time-to-live for cache entries, specified in milliseconds.
  - `autoCache?: boolean`: Enables automatic caching based on frequency of access.
  - `storageType?: "cache" | "local" | "session"`: Specifies the storage type for caching.

### `CachedEntry<T>`

- **Generic Type**: `<T>`
- **Fields**:
  - `value: T`: The data being cached.
  - `expiration: number`: Timestamp of when the entry expires.
  - `accessedAt: number`: Last access timestamp for LRU strategy.

## Constants

### `CACHE_NAME`

- **Type**: `string`
- **Value**: `"muntaha-shop-cache"`
- **Description**: Name of the cache storage used for storing entries.

### `DEFAULT_TTL`

- **Type**: `number`
- **Value**: `300000` (5 minutes in milliseconds)
- **Description**: Default time-to-live for cache entries.

### `MAX_CACHE_SIZE`

- **Type**: `number`
- **Value**: `50`
- **Description**: Maximum number of entries allowed in the cache.

## MuntahaCache Class

### Properties

- `accessCount`: A `Map<string, number>` used for tracking access frequency, aiding in auto-caching functionality.

### Methods

#### `getLocal<T>(key: CacheKey): T | null`

- **Description**: Retrieves data from `localStorage` using a specified key.
- **Parameters**: `key` - The unique identifier for the cached data.
- **Returns**: Cached data of type `T`, or `null` if not found or expired.

#### `getSession<T>(key: CacheKey): T | null`

- **Description**: Retrieves data from `sessionStorage` using a specified key.
- **Parameters**: `key` - The unique identifier for the cached data.
- **Returns**: Cached data of type `T`, or `null` if not found or expired.

#### `getCache<T>(url: string): Promise<T | null>`

- **Description**: Retrieves data from Cache Storage using a specified URL.
- **Parameters**: `url` - URL associated with the cached data.
- **Returns**: A promise resolving to cached data of type `T`, or `null` if expired or not found.

#### `get<T>(key: CacheKey, url: string, storageType: "cache" | "local" | "session" = "cache"): Promise<T | null>`

- **Description**: Retrieves cached data based on the specified storage type.
- **Parameters**:
  - `key`: Unique identifier for the data.
  - `url`: URL associated with the cached data.
  - `storageType`: Specifies the storage type, defaulting to `cache`.
- **Returns**: Cached data of type `T` or `null`.

#### `setLocal<T>(key: CacheKey, value: T, expiration: number): void`

- **Description**: Stores data in `localStorage`.
- **Parameters**:
  - `key`: Unique identifier for the data.
  - `value`: Data to cache.
  - `expiration`: Expiration time in milliseconds.

#### `setSession<T>(key: CacheKey, value: T, expiration: number): void`

- **Description**: Stores data in `sessionStorage`.
- **Parameters**:
  - `key`: Unique identifier for the data.
  - `value`: Data to cache.
  - `expiration`: Expiration time in milliseconds.

#### `setCache<T>(url: string, value: T, expiration: number): Promise<void>`

- **Description**: Stores data in Cache Storage.
- **Parameters**:
  - `url`: URL associated with the data.
  - `value`: Data to cache.
  - `expiration`: Expiration time in milliseconds.

#### `set<T>(key: CacheKey, url: string, value: T, options: CacheOptions = {}): Promise<void>`

- **Description**: Caches data with the specified key, URL, and options.
- **Parameters**:
  - `key`: Unique identifier for the data.
  - `url`: URL for the cached data.
  - `value`: Data to cache.
  - `options`: Optional caching options such as TTL, auto-cache, and storage type.

#### `delete(url: string): Promise<void>`

- **Description**: Deletes a cached entry by its URL.
- **Parameters**: `url` - The URL of the cached entry to delete.

#### `clearAll(): Promise<void>`

- **Description**: Clears all cached data across storage types.

#### `applyLRU(cache: Cache): Promise<void>`

- **Description**: Manages cache size by evicting entries based on the Least Recently Used (LRU) strategy.
- **Parameters**: `cache` - Cache object for LRU eviction.

#### `convertImageToBlob(imageUrl: string): Promise<Blob>`

- **Description**: Converts an image URL or file to a Blob for caching.
- **Parameters**: `imageUrl` - The URL of the image.
- **Returns**: A promise that resolves to a Blob representing the image.

#### `setMedia(url: string, imageUrl: string, ttl: number = DEFAULT_TTL): Promise<void>`

- **Description**: Caches an image by converting it to a Blob and storing it.
- **Parameters**:
  - `url`: URL associated with the cached image.
  - `imageUrl`: The URL of the image.
  - `ttl`: Time-to-live in milliseconds.

#### `getMedia(url: string): Promise<Blob | null>`

- **Description**: Retrieves a cached image Blob by URL.
- **Parameters**: `url` - URL of the cached image.
- **Returns**: A promise that resolves to a Blob representing the image, or `null` if expired or not found.

---

## Usage Example

```typescript
import { MuntahaCache } from "./MuntahaCache";

// 1. Cache a JSON object with a custom TTL (Time to Live)
await MuntahaCache.set(
  "myKey",
  "https://example.com/data",
  { myData: "value" },
  { ttl: 60000 } // TTL in milliseconds (e.g., 60 seconds)
);

// 2. Retrieve the cached JSON object
const cachedData = await MuntahaCache.get(
  "myKey",
  "https://example.com/data",
  "local" // Optional: Specify "local" for localStorage or "session" for sessionStorage
);

// 3. Cache an image from a URL as a Blob
await MuntahaCache.setMedia("imageKey", "https://example.com/image.png");

// 4. Retrieve the cached image Blob
const cachedImageBlob = await MuntahaCache.getMedia("imageKey");

// 5. Check if a specific cache key exists
const isCached = await MuntahaCache.has("myKey", "https://example.com/data");

// 6. Remove an item from the cache
await MuntahaCache.remove("myKey", "https://example.com/data");

// 7. Clear all cached items from local storage
await MuntahaCache.clear("local");

// 8. Clear all cached items from session storage
await MuntahaCache.clear("session");
```
