# JavaScript Object Manipulation

A comprehensive, modern guide to creating, accessing, modifying, merging, transforming, and deep-manipulating objects in JavaScript (ES6 through ES2024+).

---

## 1. Object Creation & Property Shorthands

### Modern Object Literals

```javascript
const username = "alex_dev";
const role = "Admin";
const dynamicField = "lastLoginDate";

// 1. Property shorthand & Computed property names
const user = {
  username, // Equivalent to username: username
  role,
  [dynamicField]: new Date().toISOString(), // Computed property key
  [`perm_${role.toLowerCase()}`]: true,    // Evaluated expression key

  // Method shorthand
  getSummary() {
    return `${this.username} (${this.role})`;
  }
};

console.log(user);
// {
//   username: 'alex_dev',
//   role: 'Admin',
//   lastLoginDate: '...',
//   perm_admin: true,
//   getSummary: [Function: getSummary]
// }
```

### Creating from Key-Value Pairs (`Object.fromEntries`)

```javascript
const entries = [
  ["theme", "dark"],
  ["fontSize", 16],
  ["notifications", true]
];

const settings = Object.fromEntries(entries);
console.log(settings); // { theme: 'dark', fontSize: 16, notifications: true }
```

---

## 2. Safe Access: Optional Chaining & Nullish Coalescing

### Optional Chaining (`?.`)
Safely navigates nested objects, arrays, and methods without throwing `TypeError: Cannot read properties of undefined`.

```javascript
const response = {
  data: {
    profile: {
      name: "Maria",
      socials: ["@maria_code"]
    }
  }
};

// 1. Nested property access
const city = response.data?.profile?.address?.city; // undefined (No runtime error!)

// 2. Optional array index access
const firstSocial = response.data?.profile?.socials?.[0]; // "@maria_code"
const nonExistentItem = response.data?.profile?.tags?.[0]; // undefined

// 3. Optional method call
const customFormat = response.format?.(); // undefined
```

### Nullish Coalescing (`??`) vs. Logical OR (`||`)
- `??` provides a fallback **only** when the left side is `null` or `undefined`.
- `||` provides a fallback whenever the left side is **falsy** (`0`, `""`, `false`, `NaN`, `null`, `undefined`).

```javascript
const config = {
  timeout: 0,      // 0 is a valid timeout, but falsy!
  debugMode: false, // false is a valid setting, but falsy!
  title: ""
};

// With || (BUGGY: overrides valid 0 and false!)
console.log(config.timeout || 5000);   // 5000 (WRONG)
console.log(config.debugMode || true); // true (WRONG)

// With ?? (CORRECT: preserves 0 and false)
console.log(config.timeout ?? 5000);   // 0 (CORRECT)
console.log(config.debugMode ?? true); // false (CORRECT)
console.log(config.missingKey ?? "default"); // "default"
```

---

## 3. Merging & Extending Objects

### Shallow Merging (Spread Operator `{ ... }`)

```javascript
const defaultSettings = {
  theme: "light",
  fontSize: 14,
  layout: "grid"
};

const userOverrides = {
  theme: "dark",
  fontSize: 16
};

// User properties overwrite defaults
const activeSettings = { ...defaultSettings, ...userOverrides };
console.log(activeSettings);
// { theme: 'dark', fontSize: 16, layout: 'grid' }
```

### Deep Merging Objects (Handling Nested Structures)
The spread operator only performs a **shallow** copy. Nested objects get completely overwritten rather than merged.

```javascript
// Recursive Deep Merge utility
function deepMerge(target, source) {
  const result = { ...target };

  for (const key of Object.keys(source)) {
    if (
      source[key] instanceof Object &&
      key in target &&
      target[key] instanceof Object &&
      !Array.isArray(source[key])
    ) {
      result[key] = deepMerge(target[key], source[key]);
    } else {
      result[key] = source[key];
    }
  }

  return result;
}

const defaultConfig = {
  app: { name: "Dashboard", version: "1.0" },
  features: { analytics: true, beta: false }
};

const customConfig = {
  app: { version: "2.0" },           // Only override version
  features: { beta: true }           // Only override beta
};

const merged = deepMerge(defaultConfig, customConfig);
console.log(merged);
/*
{
  app: { name: 'Dashboard', version: '2.0' },
  features: { analytics: true, beta: true }
}
*/
```

---

## 4. Immutable Updates: Adding, Updating & Deleting Keys

### Immutably Adding & Modifying Properties

```javascript
const profile = { id: 1, name: "Lucas", points: 100 };

// 1. Update points without mutating original
const updatedProfile = {
  ...profile,
  points: profile.points + 25,
  level: "Gold" // Add new property
};
```

### Immutably Omitting / Deleting Keys (Rest Destructuring)

```javascript
const account = {
  id: 42,
  username: "neo",
  passwordHash: "ab$90281#secret",
  internalToken: "token_xyz"
};

// Extract sensitive fields into variables, keep the rest in 'safeAccount'
const { passwordHash, internalToken, ...safeAccount } = account;

console.log(safeAccount);
// { id: 42, username: 'neo' } (Original untouched!)
```

### General `omit()` and `pick()` Utilities

```javascript
// Omit specified keys from an object
function omit(obj, keysToOmit) {
  const omitSet = new Set(keysToOmit);
  return Object.fromEntries(
    Object.entries(obj).filter(([key]) => !omitSet.has(key))
  );
}

// Pick only specified keys from an object
function pick(obj, keysToPick) {
  const pickSet = new Set(keysToPick);
  return Object.fromEntries(
    Object.entries(obj).filter(([key]) => pickSet.has(key))
  );
}

const userRecord = { id: 10, name: "Claire", email: "claire@web.dev", role: "admin", ip: "127.0.0.1" };

console.log(pick(userRecord, ["name", "email"]));
// { name: 'Claire', email: 'claire@web.dev' }

console.log(omit(userRecord, ["ip", "id"]));
// { name: 'Claire', email: 'claire@web.dev', role: 'admin' }
```

---

## 5. Checking & Inspecting Properties

### Modern Property Verification: `Object.hasOwn` (ES2022)

```javascript
const userObj = { name: "Tom", role: undefined };

// Object.hasOwn checks for OWN properties (not inherited from prototype)
console.log(Object.hasOwn(userObj, "name")); // true
console.log(Object.hasOwn(userObj, "role")); // true (exists, even though value is undefined!)
console.log(Object.hasOwn(userObj, "toString")); // false (inherited from Object.prototype)

// Safe on objects created without prototypes: Object.create(null)
const bareObj = Object.create(null);
bareObj.secret = 123;
console.log(Object.hasOwn(bareObj, "secret")); // true (Does not throw!)
```

### Checking for Empty Objects

```javascript
function isEmptyObject(obj) {
  return obj && Object.keys(obj).length === 0 && obj.constructor === Object;
}

console.log(isEmptyObject({})); // true
console.log(isEmptyObject({ a: 1 })); // false
```

---

## 6. Iterating & Transforming Objects

Modern object manipulation commonly leverages `Object.entries()` combined with array methods and `Object.fromEntries()`.

```
Object -> Object.entries() -> [ [key, val], ... ] -> Array.filter / map -> Object.fromEntries() -> Transformed Object
```

### Transforming Values of an Object

```javascript
const rawScores = { math: 80, physics: 90, literature: 75 };

// Add 5 bonus points to every score
const boostedScores = Object.fromEntries(
  Object.entries(rawScores).map(([subject, score]) => [subject, score + 5])
);

console.log(boostedScores); // { math: 85, physics: 95, literature: 80 }
```

### Filtering Properties by Value

```javascript
const inventory = { apples: 15, bananas: 0, oranges: 8, grapes: 0 };

// Keep only items that are in stock
const inStock = Object.fromEntries(
  Object.entries(inventory).filter(([_, qty]) => qty > 0)
);

console.log(inStock); // { apples: 15, oranges: 8 }
```

---

## 7. Deep Cloning & Immutability

### `structuredClone()` (Global Baseline Standard)
Native deep-cloning mechanism that reliably duplicates nested objects, arrays, Dates, Sets, Maps, and handles circular references.

```javascript
const userSession = {
  sessionId: "sess_99",
  createdAt: new Date(),
  preferences: { colorScheme: "dark" },
  tags: new Set(["vip", "beta-tester"])
};

const deepClone = structuredClone(userSession);

// Modifying deepClone does NOT affect userSession
deepClone.preferences.colorScheme = "light";
deepClone.tags.add("early-adopter");

console.log(userSession.preferences.colorScheme); // "dark"
console.log(userSession.tags.has("early-adopter")); // false
```

### Object Freezing & Sealing

| Method | Can Add? | Can Delete? | Can Modify Existing? |
| :--- | :--- | :--- | :--- |
| Normal Object | Yes | Yes | Yes |
| `Object.preventExtensions(obj)` | **No** | Yes | Yes |
| `Object.seal(obj)` | **No** | **No** | Yes |
| `Object.freeze(obj)` | **No** | **No** | **No** (Shallow freeze) |

#### Recursive `deepFreeze` Utility

```javascript
function deepFreeze(obj) {
  // Retrieve all property names
  Object.keys(obj).forEach((prop) => {
    if (
      typeof obj[prop] === "object" &&
      obj[prop] !== null &&
      !Object.isFrozen(obj[prop])
    ) {
      deepFreeze(obj[prop]);
    }
  });

  return Object.freeze(obj);
}

const immutableConfig = deepFreeze({
  api: { url: "https://api.example.com", key: "xyz" }
});

// In strict mode, throws TypeError: Cannot assign to read only property 'url'
// immutableConfig.api.url = "https://hack.com";
```

---

## 8. Real-World Practical Examples

### Sanitizing Request Payloads (Stripping nulls, undefined, and empty strings)

```javascript
function sanitizePayload(obj) {
  return Object.fromEntries(
    Object.entries(obj).filter(([_, value]) => {
      return value !== null && value !== undefined && value !== "";
    })
  );
}

const rawFormData = {
  name: "Julia Roberts",
  company: "",
  notes: null,
  subscribe: true,
  age: undefined
};

console.log(sanitizePayload(rawFormData));
// { name: 'Julia Roberts', subscribe: true }
```

### Flattening Nested Objects (Dot Notation Keys)

```javascript
function flattenObject(obj, prefix = "") {
  return Object.keys(obj).reduce((acc, k) => {
    const pre = prefix.length ? `${prefix}.` : "";
    if (typeof obj[k] === "object" && obj[k] !== null && !Array.isArray(obj[k])) {
      Object.assign(acc, flattenObject(obj[k], pre + k));
    } else {
      acc[pre + k] = obj[k];
    }
    return acc;
  }, {});
}

const nestedUser = {
  user: {
    profile: {
      name: "Marcus",
      address: { city: "Rome", zip: "00100" }
    }
  }
};

console.log(flattenObject(nestedUser));
// {
//   'user.profile.name': 'Marcus',
//   'user.profile.address.city': 'Rome',
//   'user.profile.address.zip': '00100'
// }
```

### Converting Object to URL Query String (and back)

```javascript
// Object to URLSearchParams
const filterParams = {
  category: "electronics",
  sort: "price_asc",
  page: 1,
  inStock: true
};

const searchParams = new URLSearchParams(filterParams);
console.log(searchParams.toString());
// "category=electronics&sort=price_asc&page=1&inStock=true"

// URLSearchParams back to plain Object
const parsedObject = Object.fromEntries(searchParams.entries());
console.log(parsedObject);
// { category: 'electronics', sort: 'price_asc', page: '1', inStock: 'true' }
```
