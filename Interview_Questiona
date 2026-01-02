# Complete Code Explanation for Interview - Mixo Ads Dashboard

## 📚 Table of Contents
1. [Project Overview](#project-overview)
2. [Tech Stack Deep Dive](#tech-stack-deep-dive)
3. [File-by-File Explanation](#file-by-file-explanation)
4. [Key Concepts Explained](#key-concepts-explained)
5. [Interview Questions & Answers](#interview-questions--answers)

---

## 🎯 Project Overview

**What is this project?**
- A React-based advertising campaign performance dashboard
- Displays campaign metrics, KPIs, and real-time updates
- Built with modern React patterns and best practices

**Why was it built?**
- To monitor and analyze advertising campaign performance
- Provide real-time insights to marketing teams
- Handle API rate limiting gracefully
- Demonstrate proficiency in React, data fetching, and state management

---

## 🛠 Tech Stack Deep Dive

### 1. **React 18**
- **What**: JavaScript library for building user interfaces
- **Why**: Component-based architecture, virtual DOM, large ecosystem
- **How**: Uses functional components with hooks (useState, useMemo, useEffect, useCallback)

### 2. **Vite**
- **What**: Next-generation frontend build tool
- **Why**: Fast HMR (Hot Module Replacement), optimized builds, ES modules
- **How**: Configured in `vite.config.js` with React plugin

### 3. **TanStack React Query (v5)**
- **What**: Powerful data synchronization library for React
- **Why**: 
  - Automatic caching
  - Background refetching
  - Request deduplication
  - Error handling & retry logic
  - Loading states management
- **How**: Wraps app in QueryClientProvider, uses `useQuery` hooks

### 4. **Server-Sent Events (SSE)**
- **What**: HTTP standard for server-to-client streaming
- **Why**: Real-time updates without polling
- **How**: EventSource API connects to `/insights/stream` endpoint

---

## 📁 File-by-File Explanation

### 📄 `package.json`

```json
{
  "name": "mixo-ads-dashboard",
  "version": "1.0.0",
  "type": "module",  // Uses ES modules (import/export syntax)
```

**Line-by-Line Explanation:**

1. **`"name"`**: Project identifier for npm
2. **`"version"`**: Semantic versioning (major.minor.patch)
3. **`"type": "module"`**: 
   - **What**: Tells Node.js to treat `.js` files as ES modules
   - **Why**: Enables modern `import/export` syntax instead of `require()`
   - **How**: Allows us to use `import React from 'react'` instead of `const React = require('react')`

```json
  "scripts": {
    "dev": "vite",           // Starts development server
    "build": "vite build",    // Creates production build
    "preview": "vite preview" // Previews production build
  },
```

**Scripts Explanation:**
- **`dev`**: Runs Vite dev server (usually on port 5173)
- **`build`**: Compiles and optimizes code for production
- **`preview`**: Serves the built files locally

```json
  "dependencies": {
    "react": "^18.2.0",                    // React library
    "react-dom": "^18.2.0",               // React DOM renderer
    "@tanstack/react-query": "^5.17.0"    // Data fetching library
  },
```

**Dependencies:**
- **`react`**: Core React library (^ means compatible with 18.2.0+)
- **`react-dom`**: Renders React components to DOM
- **`@tanstack/react-query`**: Data fetching, caching, synchronization

```json
  "devDependencies": {
    "@vitejs/plugin-react": "^4.2.1",  // Vite plugin for React
    "vite": "^5.0.8"                    // Build tool
  }
```

**DevDependencies:**
- Only needed during development, not in production
- **`@vitejs/plugin-react`**: Enables JSX transformation and HMR
- **`vite`**: The build tool itself

---

### 📄 `vite.config.js`

```javascript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
})
```

**Line-by-Line Explanation:**

1. **`import { defineConfig } from 'vite'`**
   - **What**: Imports configuration helper function
   - **Why**: Provides TypeScript autocomplete and type checking
   - **How**: Returns a config object that Vite understands

2. **`import react from '@vitejs/plugin-react'`**
   - **What**: Imports React plugin for Vite
   - **Why**: Enables JSX transformation and Fast Refresh
   - **How**: Transforms `.jsx` files to JavaScript

3. **`export default defineConfig({ plugins: [react()] })`**
   - **What**: Exports Vite configuration
   - **Why**: Tells Vite to use React plugin
   - **How**: Plugins array processes files in order

---

### 📄 `src/main.jsx` - Application Entry Point

```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import App from './App.jsx';
import './styles.css';
```

**Line-by-Line Explanation:**

1. **`import React from 'react'`**
   - **What**: Imports React library
   - **Why**: Needed for JSX (though React 17+ doesn't require it)
   - **How**: Makes React available in this file

2. **`import ReactDOM from 'react-dom/client'`**
   - **What**: Imports React DOM client API
   - **Why**: React 18 uses new `createRoot` API instead of `render`
   - **How**: Provides `createRoot` method for rendering

3. **`import { QueryClient, QueryClientProvider } from '@tanstack/react-query'`**
   - **What**: Imports React Query core components
   - **Why**: 
     - `QueryClient`: Manages cache and default options
     - `QueryClientProvider`: Makes QueryClient available to all components
   - **How**: Wraps app to enable `useQuery` hooks

4. **`import App from './App.jsx'`**
   - **What**: Imports main App component
   - **Why**: Root component of the application
   - **How**: Default export from App.jsx

5. **`import './styles.css'`**
   - **What**: Imports global CSS styles
   - **Why**: Applies styling to entire application
   - **How**: Vite processes CSS imports

```javascript
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      retry: (failureCount, error) => {
        if (error?.status === 429) {
          return failureCount < 2;  // Retry max 2 times for rate limits
        }
        return failureCount < 3;    // Retry max 3 times for other errors
      },
      refetchOnWindowFocus: false,  // Don't refetch when window regains focus
    },
  },
});
```

**QueryClient Configuration Explanation:**

1. **`new QueryClient({...})`**
   - **What**: Creates a QueryClient instance
   - **Why**: Manages all query cache and configuration
   - **How**: Stores cached data and query states

2. **`defaultOptions.queries`**
   - **What**: Default settings for all queries
   - **Why**: Avoids repeating configuration in each hook
   - **How**: Applied to every `useQuery` call

3. **`retry: (failureCount, error) => {...}`**
   - **What**: Custom retry logic function
   - **Why**: Different retry strategies for different errors
   - **How**: 
     - Receives `failureCount` (how many times failed)
     - Receives `error` (the error object)
     - Returns `true`/`false` to retry or not
   - **Logic**:
     - If 429 (rate limit): Retry up to 2 times
     - Other errors: Retry up to 3 times

4. **`refetchOnWindowFocus: false`**
   - **What**: Disables automatic refetch when window regains focus
   - **Why**: Prevents unnecessary API calls when user switches tabs
   - **How**: React Query normally refetches on focus, we disable it

```javascript
ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <QueryClientProvider client={queryClient}>
      <App />
    </QueryClientProvider>
  </React.StrictMode>
);
```

**Rendering Explanation:**

1. **`document.getElementById('root')`**
   - **What**: Gets the root DOM element from `index.html`
   - **Why**: Need a container to render React into
   - **How**: Returns the `<div id="root">` element

2. **`ReactDOM.createRoot(...)`**
   - **What**: Creates a React 18 root
   - **Why**: New concurrent rendering API
   - **How**: Returns a root object with `render` method

3. **`.render(...)`**
   - **What**: Renders React components to DOM
   - **Why**: Actually displays the app
   - **How**: Takes JSX and converts to DOM nodes

4. **`<React.StrictMode>`**
   - **What**: React development helper
   - **Why**: 
     - Detects potential problems
     - Warns about deprecated APIs
     - Double-invokes effects in dev (to catch side effects)
   - **How**: Wraps app, only active in development

5. **`<QueryClientProvider client={queryClient}>`**
   - **What**: Provides QueryClient to all child components
   - **Why**: Makes `useQuery` hooks work
   - **How**: Uses React Context API under the hood

6. **`<App />`**
   - **What**: Main application component
   - **Why**: Root of component tree
   - **How**: Renders all other components

---

### 📄 `src/api/client.js` - HTTP Client with Rate Limiting

```javascript
const API_BASE_URL = 'https://mixo-fe-backend-task.vercel.app';
```

**Line 1:**
- **What**: Base URL for all API requests
- **Why**: Single source of truth, easy to change
- **How**: Prefixed to all endpoint paths

```javascript
export class HttpError extends Error {
  constructor(status, payload) {
    super(`HTTP ${status}`);
    this.status = status;
    this.payload = payload;
    this.name = 'HttpError';
  }
}
```

**HttpError Class Explanation:**

1. **`export class HttpError extends Error`**
   - **What**: Custom error class
   - **Why**: Distinguish HTTP errors from other errors
   - **How**: Extends built-in Error class

2. **`constructor(status, payload)`**
   - **What**: Creates error with status code and response data
   - **Why**: Need to know HTTP status (404, 429, 500, etc.)
   - **How**: Stores status and payload for error handling

3. **`super(\`HTTP ${status}\`)`**
   - **What**: Calls parent Error constructor with message
   - **Why**: Error needs a message
   - **How**: Creates error message like "HTTP 429"

4. **`this.status = status`**
   - **What**: Stores HTTP status code
   - **Why**: Components need to check status (e.g., 404 vs 500)
   - **How**: Accessible as `error.status`

5. **`this.payload = payload`**
   - **What**: Stores response body
   - **Why**: May contain useful info (e.g., `retry_after` for 429)
   - **How**: Accessible as `error.payload`

```javascript
async function requestJSON(url, options = {}) {
  const { signal, maxRetries = 2, ...fetchOptions } = options;
```

**requestJSON Function Explanation:**

1. **`async function requestJSON(...)`**
   - **What**: Async function for HTTP requests
   - **Why**: Need to await fetch responses
   - **How**: Returns a Promise

2. **`const { signal, maxRetries = 2, ...fetchOptions } = options`**
   - **What**: Destructures options object
   - **Why**: 
     - `signal`: For request cancellation (AbortController)
     - `maxRetries`: How many retries (default 2)
     - `...fetchOptions`: All other options (method, headers, etc.)
   - **How**: Spread operator collects remaining properties

```javascript
  let retryCount = 0;
  
  while (retryCount <= maxRetries) {
    try {
      const response = await fetch(url, {
        ...fetchOptions,
        signal,
        headers: {
          'Content-Type': 'application/json',
          ...fetchOptions.headers,
        },
      });
```

**Request Loop Explanation:**

1. **`let retryCount = 0`**
   - **What**: Tracks retry attempts
   - **Why**: Need to limit retries
   - **How**: Incremented on each retry

2. **`while (retryCount <= maxRetries)`**
   - **What**: Retry loop
   - **Why**: Allows multiple attempts
   - **How**: Continues until success or max retries

3. **`await fetch(url, {...})`**
   - **What**: Native browser fetch API
   - **Why**: Modern, promise-based HTTP client
   - **How**: Makes HTTP request, returns Response object

4. **`signal`**
   - **What**: AbortController signal
   - **Why**: Allows cancelling requests
   - **How**: Passed to fetch, can abort request

5. **`headers: { 'Content-Type': 'application/json', ...fetchOptions.headers }`**
   - **What**: Request headers
   - **Why**: 
     - Tells server we're sending JSON
     - Allows overriding with custom headers
   - **How**: Merges default with custom headers

```javascript
      if (!response.ok) {
        const payload = await response.json().catch(() => ({}));
        
        if (response.status === 429 && retryCount < maxRetries) {
          const retryAfter = payload.retry_after || 1;
          await new Promise(resolve => setTimeout(resolve, retryAfter * 1000));
          retryCount++;
          continue;
        }
        
        throw new HttpError(response.status, payload);
      }

      return await response.json();
```

**Error Handling Explanation:**

1. **`if (!response.ok)`**
   - **What**: Checks if response is successful (200-299)
   - **Why**: Need to handle errors
   - **How**: `response.ok` is false for 4xx/5xx

2. **`const payload = await response.json().catch(() => ({}))`**
   - **What**: Tries to parse JSON response
   - **Why**: Error response may contain useful data
   - **How**: `.catch(() => ({}))` returns empty object if JSON parse fails

3. **`if (response.status === 429 && retryCount < maxRetries)`**
   - **What**: Rate limit handling
   - **Why**: API limits requests, returns 429
   - **How**: 
     - 429 = Too Many Requests
     - Only retry if haven't exceeded max

4. **`const retryAfter = payload.retry_after || 1`**
   - **What**: Gets wait time from server
   - **Why**: Server tells us how long to wait
   - **How**: Uses server value or defaults to 1 second

5. **`await new Promise(resolve => setTimeout(resolve, retryAfter * 1000))`**
   - **What**: Waits before retrying
   - **Why**: Must wait for rate limit to reset
   - **How**: 
     - Creates Promise that resolves after delay
     - `setTimeout` waits `retryAfter` seconds
     - `* 1000` converts seconds to milliseconds

6. **`retryCount++; continue;`**
   - **What**: Increments counter and retries
   - **Why**: Track attempts and loop again
   - **How**: `continue` jumps to next loop iteration

7. **`throw new HttpError(response.status, payload)`**
   - **What**: Throws custom error for non-429 errors
   - **Why**: Components need to handle different errors
   - **How**: Creates HttpError with status and payload

8. **`return await response.json()`**
   - **What**: Returns parsed JSON on success
   - **Why**: Components need data, not Response object
   - **How**: Parses response body as JSON

```javascript
    } catch (error) {
      if (error instanceof HttpError) {
        throw error;
      }
      
      if (error.name === 'AbortError') {
        throw error;
      }
      
      if (retryCount < maxRetries) {
        retryCount++;
        await new Promise(resolve => setTimeout(resolve, 1000));
        continue;
      }
      
      throw error;
    }
  }
}
```

**Catch Block Explanation:**

1. **`catch (error)`**
   - **What**: Catches any errors from fetch
   - **Why**: Network errors, JSON parse errors, etc.
   - **How**: Executes when try block throws

2. **`if (error instanceof HttpError)`**
   - **What**: Checks if it's our custom HttpError
   - **Why**: Already handled, don't retry
   - **How**: Re-throws immediately

3. **`if (error.name === 'AbortError')`**
   - **What**: Checks if request was cancelled
   - **Why**: User navigated away, don't retry
   - **How**: Re-throws immediately

4. **`if (retryCount < maxRetries)`**
   - **What**: Generic retry for network errors
   - **Why**: Network might be temporarily down
   - **How**: Waits 1 second, then retries

5. **`throw error`**
   - **What**: Re-throws if max retries reached
   - **Why**: Give up after too many failures
   - **How**: Error bubbles up to component

```javascript
export function createClient(baseURL = API_BASE_URL) {
  return {
    get: (path, options = {}) => {
      return requestJSON(`${baseURL}${path}`, {
        ...options,
        method: 'GET',
      });
    },
  };
}

export const client = createClient();
```

**Client Factory Explanation:**

1. **`export function createClient(baseURL = API_BASE_URL)`**
   - **What**: Factory function for creating API client
   - **Why**: Allows different base URLs (testing, staging, prod)
   - **How**: Returns object with HTTP methods

2. **`return { get: (path, options) => {...} }`**
   - **What**: Returns client object with `get` method
   - **Why**: Clean API: `client.get('/campaigns')`
   - **How**: Calls `requestJSON` with full URL

3. **`export const client = createClient()`**
   - **What**: Default client instance
   - **Why**: Most components use same API
   - **How**: Uses default `API_BASE_URL`

---

### 📄 `src/api/campaigns.js` - API Endpoint Functions

```javascript
import { client } from './client.js';

export async function listCampaigns(signal) {
  const response = await client.get('/campaigns', { signal });
  return response;
}
```

**listCampaigns Function:**

1. **`export async function listCampaigns(signal)`**
   - **What**: Fetches all campaigns
   - **Why**: Separates API logic from components
   - **How**: Calls `client.get` with endpoint path

2. **`signal`**
   - **What**: AbortController signal
   - **Why**: Allows cancelling request
   - **How**: Passed from React Query

3. **`return response`**
   - **What**: Returns API response
   - **Why**: React Query needs the data
   - **How**: Response is already parsed JSON

```javascript
export async function getCampaign(id, signal) {
  const response = await client.get(`/campaigns/${id}`, { signal });
  return response;
}
```

**getCampaign Function:**
- Similar to `listCampaigns` but for single campaign
- Uses template literal for dynamic ID: `/campaigns/${id}`

```javascript
export async function getAggregateInsights(signal) {
  const response = await client.get('/campaigns/insights', { signal });
  return response;
}
```

**getAggregateInsights Function:**
- Fetches aggregate KPIs for all campaigns
- Used in top KPI cards section

```javascript
export async function getCampaignInsights(id, signal) {
  const response = await client.get(`/campaigns/${id}/insights`, { signal });
  return response;
}
```

**getCampaignInsights Function:**
- Fetches insights for specific campaign
- Used in campaign detail modal

---

### 📄 `src/hooks/useCampaigns.js` - React Query Hook for Campaigns

```javascript
import { useQuery } from '@tanstack/react-query';
import { listCampaigns } from '../api/campaigns.js';

export function useCampaigns() {
  return useQuery({
    queryKey: ['campaigns'],
    queryFn: ({ signal }) => listCampaigns(signal),
    staleTime: 60 * 1000,
    retry: (failureCount, error) => {
      if (error?.status === 429) {
        return failureCount < 2;
      }
      return failureCount < 3;
    },
  });
}
```

**Line-by-Line Explanation:**

1. **`import { useQuery } from '@tanstack/react-query'`**
   - **What**: Imports React Query hook
   - **Why**: Manages data fetching, caching, loading states
   - **How**: Returns object with `data`, `isLoading`, `error`, etc.

2. **`import { listCampaigns } from '../api/campaigns.js'`**
   - **What**: Imports API function
   - **Why**: Separates data fetching logic
   - **How**: Called by React Query

3. **`export function useCampaigns()`**
   - **What**: Custom hook for campaigns
   - **Why**: Reusable, encapsulates query logic
   - **How**: Returns React Query result object

4. **`queryKey: ['campaigns']`**
   - **What**: Unique identifier for this query
   - **Why**: 
     - Used for caching
     - Used for invalidation
     - Used for deduplication
   - **How**: Array of strings/values uniquely identifies query

5. **`queryFn: ({ signal }) => listCampaigns(signal)`**
   - **What**: Function that fetches data
   - **Why**: React Query calls this to get data
   - **How**: 
     - Receives object with `signal` (AbortController)
     - Calls `listCampaigns` with signal
     - Returns Promise that resolves to data

6. **`staleTime: 60 * 1000`**
   - **What**: Time (ms) before data is considered stale
   - **Why**: 
     - Prevents unnecessary refetches
     - Data fresh for 60 seconds
     - After 60s, refetches on next access
   - **How**: 60 * 1000 = 60,000ms = 60 seconds

7. **`retry: (failureCount, error) => {...}`**
   - **What**: Custom retry logic
   - **Why**: Override default retry behavior
   - **How**: 
     - Returns `true` to retry, `false` to stop
     - For 429: retry up to 2 times
     - Other errors: retry up to 3 times

**What useCampaigns Returns:**
```javascript
{
  data: { campaigns: [...], total: 10 },  // API response
  isLoading: false,                        // Initial fetch in progress
  isFetching: false,                      // Any fetch in progress
  error: null,                            // Error object if failed
  refetch: () => {...},                   // Function to manually refetch
  // ... more properties
}
```

---

### 📄 `src/hooks/useAggregateInsights.js`

```javascript
import { useQuery } from '@tanstack/react-query';
import { getAggregateInsights } from '../api/campaigns.js';

export function useAggregateInsights() {
  return useQuery({
    queryKey: ['aggregate-insights'],
    queryFn: ({ signal }) => getAggregateInsights(signal),
    staleTime: 30 * 1000,  // 30 seconds (shorter than campaigns)
    retry: (failureCount, error) => {
      if (error?.status === 429) {
        return failureCount < 2;
      }
      return failureCount < 3;
    },
  });
}
```

**Differences from useCampaigns:**
- **`queryKey: ['aggregate-insights']`**: Different cache key
- **`staleTime: 30 * 1000`**: Shorter (30s) because insights change more frequently
- **`getAggregateInsights`**: Different endpoint

---

### 📄 `src/hooks/useCampaignDetails.js`

```javascript
import { useQuery } from '@tanstack/react-query';
import { getCampaign } from '../api/campaigns.js';

export function useCampaignDetails(id) {
  return useQuery({
    queryKey: ['campaign', id],
    queryKey: ['campaign', id],  // Includes ID in key
    queryFn: ({ signal }) => getCampaign(id, signal),
    enabled: !!id,  // Only fetch if ID exists
    retry: (failureCount, error) => {
      if (error?.status === 429) {
        return failureCount < 2;
      }
      return failureCount < 3;
    },
  });
}
```

**Key Differences:**

1. **`queryKey: ['campaign', id]`**
   - **What**: Includes ID in cache key
   - **Why**: Each campaign has separate cache entry
   - **How**: `['campaign', 1]` vs `['campaign', 2]` are different queries

2. **`enabled: !!id`**
   - **What**: Conditionally enables query
   - **Why**: Don't fetch if no ID selected
   - **How**: 
     - `!!id` converts to boolean
     - `undefined` → `false` (disabled)
     - `123` → `true` (enabled)

---

### 📄 `src/hooks/useCampaignInsights.js`

```javascript
import { useQuery } from '@tanstack/react-query';
import { getCampaignInsights } from '../api/campaigns.js';

export function useCampaignInsights(id, enabled = true) {
  return useQuery({
    queryKey: ['campaign-insights', id],
    queryFn: ({ signal }) => getCampaignInsights(id, signal),
    enabled: !!id && enabled,  // Both conditions must be true
    retry: (failureCount, error) => {
      if (error?.status === 429) {
        return failureCount < 2;
      }
      return failureCount < 3;
    },
  });
}
```

**Key Feature:**

1. **`enabled = true`**
   - **What**: Default parameter
   - **Why**: Allows disabling from parent component
   - **How**: Can pass `false` to prevent fetching

2. **`enabled: !!id && enabled`**
   - **What**: Both conditions must be true
   - **Why**: Need ID and explicit enable flag
   - **How**: Used to disable when Live Mode is active (SSE handles updates)

---

### 📄 `src/App.jsx` - Main Application Component

```javascript
import { useState, useMemo } from 'react';
import { useQueryClient } from '@tanstack/react-query';
import { useCampaigns } from './hooks/useCampaigns.js';
import { useAggregateInsights } from './hooks/useAggregateInsights.js';
import { useCampaignDetails } from './hooks/useCampaignDetails.js';
import { useCampaignInsights } from './hooks/useCampaignInsights.js';
import { AggregateKpis } from './components/AggregateKpis.jsx';
import { CampaignFilters } from './components/CampaignFilters.jsx';
import { CampaignList } from './components/CampaignList.jsx';
import { CampaignModal } from './components/CampaignModal.jsx';
```

**Imports Explanation:**

1. **`useState, useMemo`**: React hooks for state and memoization
2. **`useQueryClient`**: React Query hook to access QueryClient instance
3. **Hook imports**: Custom data fetching hooks
4. **Component imports**: UI components

```javascript
function App() {
  const [selectedCampaignId, setSelectedCampaignId] = useState(null);
  const [searchTerm, setSearchTerm] = useState('');
  const [statusFilter, setStatusFilter] = useState('all');
  const [platformFilter, setPlatformFilter] = useState('all');
  const [sortBy, setSortBy] = useState('newest');
  const [lastRefresh, setLastRefresh] = useState(new Date());
```

**State Management:**

1. **`selectedCampaignId`**
   - **What**: ID of currently selected campaign
   - **Why**: Controls which modal to show
   - **How**: `null` = no selection, `123` = campaign 123 selected

2. **`searchTerm`**
   - **What**: Search input value
   - **Why**: Filter campaigns by name
   - **How**: Updated on input change

3. **`statusFilter`**
   - **What**: Selected status filter
   - **Why**: Filter by active/paused/completed
   - **How**: 'all' or 'active' or 'paused' or 'completed'

4. **`platformFilter`**
   - **What**: Selected platform filter
   - **Why**: Filter by Meta/Google/LinkedIn
   - **How**: 'all' or platform name

5. **`sortBy`**
   - **What**: Sort option
   - **Why**: Order campaigns
   - **How**: 'newest' or 'budget-desc' or 'daily-budget-desc'

6. **`lastRefresh`**
   - **What**: Timestamp of last manual refresh
   - **Why**: Display to user
   - **How**: Updated when refresh button clicked

```javascript
  const queryClient = useQueryClient();

  const { data: campaignsData, isLoading: campaignsLoading, error: campaignsError } = useCampaigns();
  const { data: aggregateData, isLoading: aggregateLoading, error: aggregateError } = useAggregateInsights();
  const { data: campaignData, isLoading: campaignLoading, error: campaignError } = useCampaignDetails(selectedCampaignId);
  const { data: insightsData, isLoading: insightsLoading, error: insightsError } = useCampaignInsights(
    selectedCampaignId,
    !!selectedCampaignId
  );
```

**Data Fetching:**

1. **`const queryClient = useQueryClient()`**
   - **What**: Gets QueryClient instance
   - **Why**: Need to invalidate queries manually
   - **How**: Used in `handleRefresh` function

2. **`useCampaigns()`**
   - **What**: Fetches all campaigns
   - **Why**: Display in list
   - **Returns**: `{ data, isLoading, error }`

3. **`useAggregateInsights()`**
   - **What**: Fetches aggregate KPIs
   - **Why**: Display top KPI cards
   - **Returns**: `{ data, isLoading, error }`

4. **`useCampaignDetails(selectedCampaignId)`**
   - **What**: Fetches single campaign details
   - **Why**: Show in modal
   - **How**: Only fetches if `selectedCampaignId` is set

5. **`useCampaignInsights(selectedCampaignId, !!selectedCampaignId)`**
   - **What**: Fetches campaign insights
   - **Why**: Show KPIs in modal
   - **How**: Enabled when campaign selected

```javascript
  const filteredAndSortedCampaigns = useMemo(() => {
    if (!campaignsData?.campaigns) return [];

    let filtered = [...campaignsData.campaigns];

    if (searchTerm) {
      filtered = filtered.filter(c => 
        c.name.toLowerCase().includes(searchTerm.toLowerCase())
      );
    }

    if (statusFilter !== 'all') {
      filtered = filtered.filter(c => c.status === statusFilter);
    }

    if (platformFilter !== 'all') {
      filtered = filtered.filter(c => 
        c.platforms?.some(p => p.toLowerCase() === platformFilter.toLowerCase())
      );
    }

    if (sortBy === 'newest') {
      filtered.sort((a, b) => new Date(b.created_at) - new Date(a.created_at));
    } else if (sortBy === 'budget-desc') {
      filtered.sort((a, b) => (b.budget || 0) - (a.budget || 0));
    } else if (sortBy === 'daily-budget-desc') {
      filtered.sort((a, b) => (b.daily_budget || 0) - (a.daily_budget || 0));
    }

    return filtered;
  }, [campaignsData, searchTerm, statusFilter, platformFilter, sortBy]);
```

**Filtering & Sorting with useMemo:**

1. **`useMemo(() => {...}, [deps])`**
   - **What**: Memoizes computed value
   - **Why**: 
     - Prevents recalculation on every render
     - Only recalculates when dependencies change
     - Performance optimization
   - **How**: Returns memoized value

2. **`if (!campaignsData?.campaigns) return []`**
   - **What**: Early return if no data
   - **Why**: Prevents errors
   - **How**: Optional chaining (`?.`) safely accesses property

3. **`let filtered = [...campaignsData.campaigns]`**
   - **What**: Creates copy of array
   - **Why**: Don't mutate original (React best practice)
   - **How**: Spread operator creates new array

4. **Search Filter:**
   - **What**: Filters by campaign name
   - **Why**: User can search
   - **How**: Case-insensitive substring match

5. **Status Filter:**
   - **What**: Filters by status
   - **Why**: Show only active/paused/completed
   - **How**: Exact match

6. **Platform Filter:**
   - **What**: Filters by platform
   - **Why**: Show only Meta/Google/etc campaigns
   - **How**: 
     - `platforms?.some(...)` checks if array contains platform
     - Case-insensitive comparison

7. **Sorting:**
   - **`newest`**: Sort by `created_at` date (newest first)
   - **`budget-desc`**: Sort by budget (highest first)
   - **`daily-budget-desc`**: Sort by daily budget (highest first)
   - **`|| 0`**: Default to 0 if undefined

```javascript
  const handleRefresh = async () => {
    const promises = [
      queryClient.invalidateQueries({ queryKey: ['aggregate-insights'] }),
      queryClient.invalidateQueries({ queryKey: ['campaigns'] }),
    ];

    if (selectedCampaignId) {
      promises.push(
        queryClient.invalidateQueries({ queryKey: ['campaign', selectedCampaignId] }),
        queryClient.invalidateQueries({ queryKey: ['campaign-insights', selectedCampaignId] })
      );
    }

    await Promise.all(promises);
    setLastRefresh(new Date());
  };
```

**Manual Refresh Function:**

1. **`handleRefresh`**
   - **What**: Manually refreshes all data
   - **Why**: User can force update
   - **How**: Invalidates queries, triggers refetch

2. **`queryClient.invalidateQueries({ queryKey: [...] })`**
   - **What**: Marks queries as stale
   - **Why**: Forces refetch
   - **How**: Returns Promise

3. **`Promise.all(promises)`**
   - **What**: Waits for all invalidations
   - **Why**: Ensure all refetches complete
   - **How**: Resolves when all Promises resolve

4. **`setLastRefresh(new Date())`**
   - **What**: Updates refresh timestamp
   - **Why**: Show user when last refreshed
   - **How**: Creates new Date object

```javascript
  return (
    <div className="app">
      <header className="app-header">
        <h1>Campaign Dashboard</h1>
        <div className="last-refresh">
          Last refresh: {lastRefresh.toLocaleTimeString()}
        </div>
      </header>

      <div className="aggregate-section">
        <AggregateKpis
          insights={aggregateData?.insights}
          isLoading={aggregateLoading}
          error={aggregateError}
        />
      </div>

      <div className="main-content">
        <div className="campaigns-panel">
          <CampaignFilters
            searchTerm={searchTerm}
            onSearchChange={setSearchTerm}
            statusFilter={statusFilter}
            onStatusChange={setStatusFilter}
            platformFilter={platformFilter}
            onPlatformChange={setPlatformFilter}
            sortBy={sortBy}
            onSortChange={setSortBy}
          />
          <CampaignList
            campaigns={filteredAndSortedCampaigns}
            selectedId={selectedCampaignId}
            onSelect={setSelectedCampaignId}
            isLoading={campaignsLoading}
            error={campaignsError}
          />
        </div>
      </div>

      <CampaignModal
        campaign={campaignData?.campaign}
        insights={insightsData?.insights}
        isLoading={campaignLoading || insightsLoading}
        error={campaignError || insightsError}
        onRefresh={handleRefresh}
        isRefreshing={campaignLoading || insightsLoading}
        isOpen={!!selectedCampaignId}
        onClose={() => setSelectedCampaignId(null)}
      />
    </div>
  );
}
```

**JSX Return:**

1. **Component Structure:**
   - Header with title and refresh time
   - Aggregate KPIs section
   - Main content with filters and list
   - Modal (conditionally rendered)

2. **Props Passing:**
   - State values passed down
   - Setter functions passed as callbacks
   - Data from hooks passed to components

3. **Conditional Rendering:**
   - `isOpen={!!selectedCampaignId}`: Modal only open if campaign selected
   - `onClose={() => setSelectedCampaignId(null)}`: Closes modal

---

### 📄 `src/components/AggregateKpis.jsx`

```javascript
import { KpiCard } from './KpiCard.jsx';

export function AggregateKpis({ insights, isLoading, error }) {
  if (isLoading) {
    return <div className="aggregate-kpis loading">Loading aggregate insights...</div>;
  }
  
  if (error) {
    return <div className="aggregate-kpis error">Error loading insights</div>;
  }
  
  if (!insights) {
    return null;
  }
  
  return (
    <div className="aggregate-kpis">
      <KpiCard label="Total Campaigns" value={insights.total_campaigns} />
      <KpiCard label="Active Campaigns" value={insights.active_campaigns} />
      <KpiCard label="Total Impressions" value={insights.total_impressions} />
      <KpiCard label="Total Clicks" value={insights.total_clicks} />
      <KpiCard label="Total Conversions" value={insights.total_conversions} />
      <KpiCard label="Total Spend" value={insights.total_spend} format="currency" />
      <KpiCard label="Avg CTR" value={insights.avg_ctr} format="percent" />
      <KpiCard label="Avg CPC" value={insights.avg_cpc} format="currency" />
      <KpiCard label="Avg Conversion Rate" value={insights.avg_conversion_rate} format="percent" />
    </div>
  );
}
```

**Component Explanation:**

1. **Props Destructuring:**
   - `insights`: Data object from API
   - `isLoading`: Loading state
   - `error`: Error object

2. **Early Returns (Guard Clauses):**
   - **Loading**: Shows loading message
   - **Error**: Shows error message
   - **No Data**: Returns null (renders nothing)

3. **KPI Cards:**
   - 9 different metrics
   - `format` prop controls display:
     - Default: number (formatted with commas)
     - `"currency"`: Shows as currency ($X.XX)
     - `"percent"`: Shows as percentage (X.XX%)

---

### 📄 `src/components/KpiCard.jsx`

```javascript
export function KpiCard({ label, value, format = 'number' }) {
  let displayValue = value;
  
  if (format === 'currency') {
    displayValue = typeof value === 'number' ? value.toFixed(2) : '0.00';
  } else if (format === 'percent') {
    displayValue = typeof value === 'number' ? (value * 100).toFixed(2) + '%' : '0.00%';
  } else if (format === 'number') {
    displayValue = typeof value === 'number' ? value.toLocaleString() : '0';
  }
  
  return (
    <div className="kpi-card">
      <div className="kpi-label">{label}</div>
      <div className="kpi-value">{displayValue}</div>
    </div>
  );
}
```

**Line-by-Line Explanation:**

1. **`export function KpiCard({ label, value, format = 'number' })`**
   - **What**: Reusable KPI card component
   - **Why**: DRY principle (Don't Repeat Yourself)
   - **How**: Accepts props, `format` defaults to 'number'

2. **`let displayValue = value;`**
   - **What**: Initialize with raw value
   - **Why**: Will format based on `format` prop
   - **How**: Modified in if/else blocks

3. **Currency Formatting:**
   - **`value.toFixed(2)`**: Rounds to 2 decimal places
   - **`typeof value === 'number'`**: Type check
   - **`'0.00'`**: Default if not number

4. **Percent Formatting:**
   - **`value * 100`**: Converts decimal to percentage (0.15 → 15)
   - **`.toFixed(2) + '%'`**: Formats and adds % symbol
   - **`'0.00%'`**: Default if not number

5. **Number Formatting:**
   - **`value.toLocaleString()`**: Adds commas (1000 → "1,000")
   - **`'0'`**: Default if not number

6. **JSX Return:**
   - Two divs: label and value
   - CSS classes for styling

---

### 📄 `src/components/CampaignFilters.jsx`

```javascript
export function CampaignFilters({ 
  searchTerm, 
  onSearchChange, 
  statusFilter, 
  onStatusChange, 
  platformFilter, 
  onPlatformChange, 
  sortBy, 
  onSortChange 
}) {
  return (
    <div className="campaign-filters">
      <div className="filters-header">
        <h3>Filters & Search</h3>
      </div>
      <div className="filters-grid">
        <div className="filter-group">
          <label className="filter-label">Search</label>
          <input
            type="text"
            placeholder="Search campaigns..."
            value={searchTerm}
            onChange={(e) => onSearchChange(e.target.value)}
            className="filter-input"
          />
        </div>
        
        <div className="filter-group">
          <label className="filter-label">Status</label>
          <select
            value={statusFilter}
            onChange={(e) => onStatusChange(e.target.value)}
            className="filter-select"
          >
            <option value="all">All Status</option>
            <option value="active">Active</option>
            <option value="paused">Paused</option>
            <option value="completed">Completed</option>
          </select>
        </div>
        
        <div className="filter-group">
          <label className="filter-label">Platform</label>
          <select
            value={platformFilter}
            onChange={(e) => onPlatformChange(e.target.value)}
            className="filter-select"
          >
            <option value="all">All Platforms</option>
            <option value="meta">Meta</option>
            <option value="google">Google</option>
            <option value="linkedin">LinkedIn</option>
            <option value="other">Other</option>
          </select>
        </div>
        
        <div className="filter-group">
          <label className="filter-label">Sort By</label>
          <select
            value={sortBy}
            onChange={(e) => onSortChange(e.target.value)}
            className="filter-select"
          >
            <option value="newest">Newest First</option>
            <option value="budget-desc">Budget (High to Low)</option>
            <option value="daily-budget-desc">Daily Budget (High to Low)</option>
          </select>
        </div>
      </div>
    </div>
  );
}
```

**Component Explanation:**

1. **Controlled Components:**
   - All inputs are "controlled" (value comes from props)
   - `value={searchTerm}`: Input shows current state
   - `onChange={(e) => onSearchChange(e.target.value)}`: Updates state on change

2. **Search Input:**
   - Text input for searching
   - `e.target.value`: Gets input value from event

3. **Status Select:**
   - Dropdown for status filter
   - Options: All, Active, Paused, Completed

4. **Platform Select:**
   - Dropdown for platform filter
   - Options: All, Meta, Google, LinkedIn, Other

5. **Sort Select:**
   - Dropdown for sorting
   - Options: Newest, Budget, Daily Budget

---

### 📄 `src/components/CampaignList.jsx`

```javascript
export function CampaignList({ campaigns, selectedId, onSelect, isLoading, error }) {
  if (isLoading) {
    return <div className="campaign-list loading">Loading campaigns...</div>;
  }
  
  if (error) {
    return (
      <div className="campaign-list error">
        <p>Error loading campaigns</p>
        <button onClick={() => window.location.reload()}>Retry</button>
      </div>
    );
  }
  
  if (!campaigns || campaigns.length === 0) {
    return <div className="campaign-list empty">No campaigns found</div>;
  }
  
  return (
    <div className="campaign-list">
      <div className="campaign-list-header">
        <h3>Campaigns ({campaigns.length})</h3>
      </div>
      {campaigns.map((campaign) => (
        <div
          key={campaign.id}
          className={`campaign-item ${selectedId === campaign.id ? 'selected' : ''}`}
          onClick={() => onSelect(campaign.id)}
        >
          <div className="campaign-item-header">
            <h3>{campaign.name}</h3>
            <span className={`status-badge status-${campaign.status}`}>
              {campaign.status}
            </span>
          </div>
          <div className="campaign-item-platforms">
            {campaign.platforms?.join(', ') || 'N/A'}
          </div>
          <div className="campaign-item-budget">
            Daily Budget: ${campaign.daily_budget?.toFixed(2) || '0.00'}
          </div>
        </div>
      ))}
    </div>
  );
}
```

**Component Explanation:**

1. **State Handling:**
   - Loading: Shows loading message
   - Error: Shows error with retry button
   - Empty: Shows "No campaigns found"
   - Success: Renders list

2. **List Rendering:**
   - `campaigns.map(...)`: Iterates over array
   - `key={campaign.id}`: React key for list items
   - **Why key?**: Helps React identify which items changed

3. **Conditional Styling:**
   - `className={...selectedId === campaign.id ? 'selected' : ''}`
   - Adds 'selected' class if campaign is selected
   - Highlights selected item

4. **Click Handler:**
   - `onClick={() => onSelect(campaign.id)}`
   - Calls parent's `onSelect` with campaign ID
   - Opens modal

5. **Data Display:**
   - Campaign name
   - Status badge (color-coded)
   - Platforms (joined with commas)
   - Daily budget (formatted as currency)

6. **Optional Chaining:**
   - `campaign.platforms?.join(', ')`: Safe array access
   - `campaign.daily_budget?.toFixed(2)`: Safe number access
   - Prevents errors if property is undefined

---

### 📄 `src/components/CampaignModal.jsx`

```javascript
import { KpiCard } from './KpiCard.jsx';
import { LiveStream } from './LiveStream.jsx';
import { useState, useCallback, useEffect } from 'react';

export function CampaignModal({ campaign, insights, isLoading, error, onRefresh, isRefreshing, isOpen, onClose }) {
  const [liveMode, setLiveMode] = useState(false);
  const [liveInsights, setLiveInsights] = useState(null);
```

**Component Setup:**

1. **Props:**
   - `campaign`: Campaign data
   - `insights`: Campaign insights/KPIs
   - `isLoading`: Loading state
   - `error`: Error object
   - `onRefresh`: Refresh function
   - `isRefreshing`: Refresh in progress
   - `isOpen`: Whether modal is open
   - `onClose`: Close handler

2. **Local State:**
   - `liveMode`: Whether Live Mode is enabled
   - `liveInsights`: Real-time insights from SSE

```javascript
  const handleLiveUpdate = useCallback((data) => {
    if (data.insights) {
      setLiveInsights(data.insights);
    }
  }, []);
```

**Live Update Handler:**

1. **`useCallback`**
   - **What**: Memoizes function
   - **Why**: Prevents re-creating function on every render
   - **How**: Returns same function reference if dependencies don't change

2. **`handleLiveUpdate`**
   - **What**: Callback for SSE updates
   - **Why**: Updates local state with real-time data
   - **How**: Called by LiveStream component

```javascript
  useEffect(() => {
    const handleEscape = (e) => {
      if (e.key === 'Escape' && isOpen) {
        onClose();
      }
    };

    if (isOpen) {
      document.addEventListener('keydown', handleEscape);
      document.body.style.overflow = 'hidden';
    }

    return () => {
      document.removeEventListener('keydown', handleEscape);
      document.body.style.overflow = 'unset';
    };
  }, [isOpen, onClose]);
```

**useEffect for Modal Behavior:**

1. **`useEffect(() => {...}, [isOpen, onClose])`**
   - **What**: Side effect hook
   - **Why**: Handle modal open/close behavior
   - **How**: Runs when `isOpen` or `onClose` changes

2. **`handleEscape`**
   - **What**: Keyboard event handler
   - **Why**: Close modal on ESC key
   - **How**: Checks if key is 'Escape' and modal is open

3. **`document.addEventListener('keydown', handleEscape)`**
   - **What**: Listens for keyboard events
   - **Why**: Global keyboard listener
   - **How**: Attached when modal opens

4. **`document.body.style.overflow = 'hidden'`**
   - **What**: Prevents body scrolling
   - **Why**: Modal open = no background scroll
   - **How**: Sets CSS overflow property

5. **Cleanup Function:**
   - **What**: `return () => {...}`
   - **Why**: Clean up when component unmounts or dependencies change
   - **How**: 
     - Removes event listener
     - Restores body scroll

```javascript
  const displayInsights = liveMode && liveInsights ? liveInsights : insights;

  if (!isOpen) return null;
```

**Display Logic:**

1. **`displayInsights`**
   - **What**: Determines which insights to show
   - **Why**: Live Mode overrides regular insights
   - **How**: 
     - If Live Mode ON and liveInsights exists → use liveInsights
     - Otherwise → use regular insights

2. **`if (!isOpen) return null`**
   - **What**: Early return if modal closed
   - **Why**: Don't render if not open
   - **How**: Returns null (renders nothing)

```javascript
  return (
    <>
      <div className="modal-overlay" onClick={onClose}></div>
      <div className="modal-container">
        <div className="modal-content" onClick={(e) => e.stopPropagation()}>
```

**Modal Structure:**

1. **Fragment (`<>...</>`)**
   - **What**: Wraps multiple elements
   - **Why**: React needs single parent
   - **How**: Doesn't create DOM element

2. **`modal-overlay`**
   - **What**: Dark backdrop
   - **Why**: Visual separation, click to close
   - **How**: `onClick={onClose}` closes modal

3. **`modal-container`**
   - **What**: Centering container
   - **Why**: Centers modal on screen
   - **How**: Flexbox centering

4. **`modal-content`**
   - **What**: Actual modal box
   - **Why**: Contains modal content
   - **How**: `onClick={(e) => e.stopPropagation()}` prevents closing when clicking inside

```javascript
          <div className="modal-header">
            <h2>{campaign?.name || 'Campaign Details'}</h2>
            <button className="modal-close" onClick={onClose} aria-label="Close">
              <svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" strokeWidth="2">
                <path d="M15 5L5 15M5 5l10 10" />
              </svg>
            </button>
          </div>
```

**Modal Header:**

1. **Title:**
   - `{campaign?.name || 'Campaign Details'}`
   - Shows campaign name or default text

2. **Close Button:**
   - X icon (SVG)
   - `aria-label="Close"`: Accessibility
   - `onClick={onClose}`: Closes modal

```javascript
          <div className="modal-body">
            {isLoading && !campaign && (
              <div className="loading">
                <div>Loading campaign details...</div>
              </div>
            )}

            {error && !isLoading && (
              <div className="error">
                {error.status === 404 ? (
                  <>
                    <h3>Campaign Not Found</h3>
                    <p>The selected campaign could not be found.</p>
                  </>
                ) : (
                  <>
                    <p>Error loading campaign details</p>
                    <button onClick={onRefresh}>Retry</button>
                  </>
                )}
              </div>
            )}
```

**Loading & Error States:**

1. **Loading:**
   - Shows when `isLoading` is true AND no campaign data yet
   - Prevents showing loading after data loads

2. **Error:**
   - Shows when error exists and not loading
   - **404**: Special message "Campaign Not Found"
   - **Other**: Generic error with retry button

```javascript
            {campaign && !error && (
              <>
                <LiveStream
                  campaignId={campaign.id}
                  enabled={liveMode}
                  onUpdate={handleLiveUpdate}
                />

                <div className="campaign-info">
                  <div className="info-row">
                    <span className="info-label">ID:</span>
                    <span className="info-value">{campaign.id}</span>
                  </div>
                  <div className="info-row">
                    <span className="info-label">Status:</span>
                    <span className={`status-badge status-${campaign.status}`}>
                      {campaign.status}
                    </span>
                  </div>
                  {/* ... more info rows ... */}
                </div>
```

**Campaign Info Display:**

1. **LiveStream Component:**
   - Handles SSE connection
   - Only active when `liveMode` is true
   - Calls `handleLiveUpdate` on new data

2. **Info Rows:**
   - Key-value pairs
   - Label on left, value on right
   - Status badge with color coding

```javascript
                {displayInsights && (
                  <div className="campaign-insights">
                    <div className="insights-header">
                      <h3>Campaign Insights</h3>
                      <div className="insights-actions">
                        <label className="live-toggle">
                          <input
                            type="checkbox"
                            checked={liveMode}
                            onChange={(e) => setLiveMode(e.target.checked)}
                          />
                          <span className={liveMode ? 'live-indicator' : ''}>
                            {liveMode ? 'Live' : 'Live Mode'}
                          </span>
                        </label>
                        <button onClick={onRefresh} disabled={isRefreshing} className={isRefreshing ? 'refreshing' : ''}>
                          {isRefreshing ? (
                            <>
                              <span className="refresh-spinner"></span>
                              Refreshing...
                            </>
                          ) : (
                            'Refresh'
                          )}
                        </button>
                      </div>
                    </div>
                    {displayInsights.timestamp && (
                      <div className="insights-timestamp">
                        Updated at: {new Date(displayInsights.timestamp).toLocaleString()}
                      </div>
                    )}
                    <div className="insights-grid">
                      <KpiCard label="Impressions" value={displayInsights.impressions} />
                      <KpiCard label="Clicks" value={displayInsights.clicks} />
                      {/* ... more KPI cards ... */}
                    </div>
                  </div>
                )}
```

**Insights Section:**

1. **Live Mode Toggle:**
   - Checkbox input
   - `checked={liveMode}`: Controlled component
   - Shows "Live" when enabled (with pulsing indicator)

2. **Refresh Button:**
   - `disabled={isRefreshing}`: Disabled during refresh
   - Shows spinner when refreshing
   - Calls `onRefresh` prop

3. **Timestamp:**
   - Shows when insights were last updated
   - Formatted with `toLocaleString()`

4. **KPI Grid:**
   - 7 KPI cards
   - Uses `displayInsights` (live or regular)

---

### 📄 `src/components/LiveStream.jsx` - Server-Sent Events Handler

```javascript
import { useEffect, useRef, useState } from 'react';

export function LiveStream({ campaignId, enabled, onUpdate }) {
  const eventSourceRef = useRef(null);
  const [error, setError] = useState(null);
  const reconnectTimeoutRef = useRef(null);
  const reconnectAttemptsRef = useRef(0);
  const maxReconnectAttempts = 2;
```

**Component Setup:**

1. **Props:**
   - `campaignId`: ID of campaign to stream
   - `enabled`: Whether streaming is enabled
   - `onUpdate`: Callback for new data

2. **Refs:**
   - **`eventSourceRef`**: Stores EventSource instance
   - **Why useRef?**: Persists across renders, doesn't trigger re-render
   - **`reconnectTimeoutRef`**: Stores timeout ID for reconnection
   - **`reconnectAttemptsRef`**: Tracks reconnection attempts

3. **State:**
   - `error`: Error message if connection fails

```javascript
  useEffect(() => {
    if (!enabled || !campaignId) {
      if (eventSourceRef.current) {
        eventSourceRef.current.close();
        eventSourceRef.current = null;
      }
      return;
    }
```

**useEffect - Cleanup on Disable:**

1. **`useEffect(() => {...}, [enabled, campaignId, onUpdate])`**
   - **What**: Runs when dependencies change
   - **Why**: Manage SSE connection lifecycle
   - **How**: Connects/disconnects based on `enabled`

2. **Early Return:**
   - If disabled or no campaignId, close connection
   - Prevents unnecessary connections

```javascript
    const connect = () => {
      if (eventSourceRef.current) {
        eventSourceRef.current.close();
      }

      const url = `https://mixo-fe-backend-task.vercel.app/campaigns/${campaignId}/insights/stream`;
      const eventSource = new EventSource(url);
      eventSourceRef.current = eventSource;
```

**Connect Function:**

1. **`connect`**
   - **What**: Establishes SSE connection
   - **Why**: Reusable for initial connect and reconnection
   - **How**: Creates new EventSource

2. **Close Existing:**
   - Closes previous connection if exists
   - Prevents multiple connections

3. **`new EventSource(url)`**
   - **What**: Browser API for SSE
   - **Why**: One-way server-to-client streaming
   - **How**: Automatically connects and listens for events

4. **Store Reference:**
   - Saves to ref for cleanup

```javascript
      eventSource.onmessage = (event) => {
        try {
          const data = JSON.parse(event.data);
          onUpdate(data);
          setError(null);
          reconnectAttemptsRef.current = 0;
        } catch (err) {
          console.error('Error parsing SSE data:', err);
        }
      };
```

**Message Handler:**

1. **`eventSource.onmessage`**
   - **What**: Handler for incoming messages
   - **Why**: Receives data from server
   - **How**: Called when server sends data

2. **`JSON.parse(event.data)`**
   - **What**: Parses JSON string
   - **Why**: Server sends JSON strings
   - **How**: Converts to JavaScript object

3. **`onUpdate(data)`**
   - **What**: Calls parent's update handler
   - **Why**: Updates parent component state
   - **How**: Passes parsed data

4. **Reset Error & Attempts:**
   - Clears error on successful message
   - Resets reconnection counter

```javascript
      eventSource.onerror = (err) => {
        console.error('SSE error:', err);
        setError('Connection error');
        
        if (eventSource.readyState === EventSource.CLOSED) {
          if (reconnectAttemptsRef.current < maxReconnectAttempts) {
            reconnectAttemptsRef.current++;
            reconnectTimeoutRef.current = setTimeout(() => {
              connect();
            }, 2000);
          } else {
            setError('Max reconnection attempts reached');
          }
        }
      };
```

**Error Handler:**

1. **`eventSource.onerror`**
   - **What**: Handler for connection errors
   - **Why**: Network issues, server errors, etc.
   - **How**: Called when error occurs

2. **`eventSource.readyState === EventSource.CLOSED`**
   - **What**: Checks if connection is closed
   - **Why**: Only reconnect if actually closed
   - **How**: `readyState` can be CONNECTING, OPEN, or CLOSED

3. **Reconnection Logic:**
   - Checks if under max attempts
   - Increments counter
   - Waits 2 seconds, then reconnects
   - If max reached, shows error

```javascript
    connect();

    return () => {
      if (reconnectTimeoutRef.current) {
        clearTimeout(reconnectTimeoutRef.current);
      }
      if (eventSourceRef.current) {
        eventSourceRef.current.close();
        eventSourceRef.current = null;
      }
      reconnectAttemptsRef.current = 0;
    };
  }, [enabled, campaignId, onUpdate]);
```

**Cleanup:**

1. **`connect()`**
   - Calls connect function when effect runs

2. **Cleanup Function:**
   - Clears reconnection timeout
   - Closes EventSource connection
   - Resets reconnection attempts
   - Prevents memory leaks

```javascript
  return error ? <div className="live-stream-error">{error}</div> : null;
}
```

**Error Display:**
- Shows error message if connection fails
- Returns null if no error (component is invisible)

---

## 🔑 Key Concepts Explained

### 1. **React Hooks**

#### useState
- **What**: Manages component state
- **Why**: Components need to remember values between renders
- **How**: `const [value, setValue] = useState(initialValue)`
- **Example**: `const [searchTerm, setSearchTerm] = useState('')`

#### useEffect
- **What**: Performs side effects (API calls, subscriptions, DOM manipulation)
- **Why**: Need to run code after render
- **How**: `useEffect(() => { effect }, [dependencies])`
- **Cleanup**: Return function for cleanup

#### useMemo
- **What**: Memoizes computed values
- **Why**: Avoid expensive recalculations
- **How**: `useMemo(() => compute(), [deps])`
- **Example**: Filtered campaigns list

#### useCallback
- **What**: Memoizes functions
- **Why**: Prevents unnecessary re-renders
- **How**: `useCallback(() => fn(), [deps])`
- **Example**: `handleLiveUpdate` callback

#### useRef
- **What**: Mutable reference that persists across renders
- **Why**: Store values without causing re-render
- **How**: `const ref = useRef(initialValue)`
- **Example**: `eventSourceRef` for EventSource

### 2. **TanStack React Query**

#### QueryClient
- **What**: Central cache and configuration manager
- **Why**: Manages all queries in application
- **How**: Created once, provided via Context

#### useQuery Hook
- **What**: Fetches and caches data
- **Why**: Automatic caching, loading states, error handling
- **How**: `useQuery({ queryKey, queryFn, ...options })`
- **Returns**: `{ data, isLoading, error, refetch, ... }`

#### Query Keys
- **What**: Unique identifier for cached data
- **Why**: Used for caching, invalidation, deduplication
- **How**: Array of strings/values: `['campaigns']` or `['campaign', id]`

#### Stale Time
- **What**: Time before data is considered stale
- **Why**: Prevents unnecessary refetches
- **How**: `staleTime: 60 * 1000` (60 seconds)

#### Query Invalidation
- **What**: Marks queries as stale, triggers refetch
- **Why**: Force refresh after mutations
- **How**: `queryClient.invalidateQueries({ queryKey: [...] })`

### 3. **Server-Sent Events (SSE)**

#### What is SSE?
- **What**: HTTP standard for server-to-client streaming
- **Why**: Real-time updates without polling
- **How**: Server sends events, client listens

#### EventSource API
- **What**: Browser API for SSE
- **Why**: Simple one-way streaming
- **How**: `new EventSource(url)`

#### vs WebSockets
- **SSE**: One-way (server → client), simpler, HTTP-based
- **WebSockets**: Two-way, more complex, TCP-based

### 4. **Rate Limiting Handling**

#### Problem
- API limits to 10 requests/minute
- Returns 429 status with `retry_after` header

#### Solution
- Detect 429 status
- Read `retry_after` from response
- Wait specified time before retry
- Maximum 2 retries

### 5. **Request Cancellation**

#### AbortController
- **What**: Browser API for cancelling requests
- **Why**: Cancel requests when component unmounts or user navigates
- **How**: 
  - Create `AbortController`
  - Pass `signal` to fetch
  - Call `abort()` to cancel

#### React Query Integration
- React Query automatically passes `signal` to `queryFn`
- Cancels when query is disabled or component unmounts

### 6. **Component Patterns**

#### Controlled Components
- **What**: Input value controlled by React state
- **Why**: Single source of truth
- **How**: `value={state}` and `onChange={(e) => setState(e.target.value)}`

#### Conditional Rendering
- **What**: Render different content based on conditions
- **Why**: Show loading, error, or data states
- **How**: `{condition && <Component />}` or `{condition ? <A /> : <B />}`

#### Props Drilling
- **What**: Passing props through multiple components
- **Why**: Need data in nested component
- **How**: Pass props from parent to child

#### Composition
- **What**: Building complex UIs from simple components
- **Why**: Reusability, maintainability
- **How**: Combine small components into larger ones

---

## 💬 Interview Questions & Answers

### Q1: Why did you choose TanStack React Query?

**Answer:**
- **Automatic caching**: Reduces API calls, improves performance
- **Loading states**: Built-in `isLoading`, `isFetching` states
- **Error handling**: Automatic retry logic, error states
- **Request deduplication**: Multiple components requesting same data = one request
- **Background refetching**: Keeps data fresh automatically
- **Request cancellation**: Cancels requests on unmount automatically

### Q2: How does rate limiting work in your code?

**Answer:**
1. API returns 429 status when rate limit exceeded
2. Response includes `retry_after` field (seconds to wait)
3. HTTP client detects 429 status
4. Waits for `retry_after` seconds
5. Retries request (max 2 times)
6. If still fails, throws error

**Code Location**: `src/api/client.js` lines 31-36

### Q3: Explain the filtering and sorting logic.

**Answer:**
1. Uses `useMemo` to memoize filtered/sorted list
2. Only recalculates when dependencies change (campaigns, filters, sort)
3. **Filtering sequence**:
   - Search filter (name contains search term)
   - Status filter (exact match)
   - Platform filter (array contains platform)
4. **Sorting**:
   - Newest: Sort by `created_at` date (descending)
   - Budget: Sort by `budget` (descending)
   - Daily Budget: Sort by `daily_budget` (descending)

**Code Location**: `src/App.jsx` lines 30-60

### Q4: How does the Live Mode (SSE) work?

**Answer:**
1. User toggles "Live Mode" checkbox
2. `LiveStream` component receives `enabled={true}`
3. Creates `EventSource` connection to `/insights/stream` endpoint
4. Server sends updates as JSON events
5. Component parses JSON and calls `onUpdate` callback
6. Parent component updates state with new insights
7. UI reflects changes immediately
8. On error: Attempts reconnection (max 2 times)
9. On disable: Closes connection, cleans up

**Code Location**: `src/components/LiveStream.jsx`

### Q5: Why use useMemo for filtering?

**Answer:**
- **Performance**: Filtering/sorting can be expensive with large lists
- **Prevents unnecessary work**: Only recalculates when dependencies change
- **Without useMemo**: Would recalculate on every render (even if filters didn't change)
- **With useMemo**: Only recalculates when campaigns, searchTerm, filters, or sortBy change

**Code Location**: `src/App.jsx` line 30

### Q6: How do you handle request cancellation?

**Answer:**
1. React Query automatically creates `AbortController` for each query
2. Passes `signal` to `queryFn`
3. API functions accept `signal` parameter
4. Pass `signal` to `fetch()` call
5. When query is disabled or component unmounts, React Query cancels request
6. Fetch automatically aborts, preventing unnecessary network calls

**Code Location**: 
- Hooks pass signal: `src/hooks/useCampaigns.js` line 7
- API uses signal: `src/api/campaigns.js` line 4
- Client passes signal: `src/api/client.js` line 21

### Q7: What's the difference between isLoading and isFetching?

**Answer:**
- **`isLoading`**: `true` only on initial fetch (no cached data)
- **`isFetching`**: `true` on any fetch (including background refetches)
- **Example**: 
  - First load: Both are `true`
  - Background refetch: `isLoading = false`, `isFetching = true`
  - Cached data shown: Both are `false`

### Q8: Explain the component hierarchy.

**Answer:**
```
App (main component)
├── Header (title + refresh time)
├── AggregateKpis (9 KPI cards)
└── Main Content
    └── Campaigns Panel
        ├── CampaignFilters (search, filters, sort)
        └── CampaignList (clickable campaign items)
    └── CampaignModal (conditionally rendered)
        ├── Campaign Info (details)
        ├── Campaign Insights (7 KPI cards)
        └── LiveStream (SSE handler)
```

### Q9: How does error handling work?

**Answer:**
1. **HTTP Client**: Catches fetch errors, throws `HttpError` with status
2. **React Query**: Catches errors, provides `error` in hook result
3. **Components**: Check `error` prop, display error UI
4. **Retry Logic**: 
   - 429 errors: Retry up to 2 times
   - Other errors: Retry up to 3 times
5. **User Actions**: Retry button in error UI

**Code Locations**:
- HTTP client: `src/api/client.js`
- Query retry: `src/hooks/useCampaigns.js` lines 9-14
- Error UI: `src/components/CampaignList.jsx` lines 6-13

### Q10: Why separate API functions from hooks?

**Answer:**
- **Separation of Concerns**: API logic separate from React logic
- **Reusability**: API functions can be used outside React
- **Testability**: Easier to test API functions independently
- **Maintainability**: Changes to API don't affect React code
- **Single Responsibility**: Each function has one job

**Structure**:
- `src/api/`: Pure API functions (no React)
- `src/hooks/`: React Query hooks (use API functions)

---

## 🎓 Additional Topics to Mention

### Performance Optimizations
- `useMemo` for expensive computations
- `useCallback` for function memoization
- React Query caching reduces API calls
- Request cancellation prevents unnecessary requests

### Accessibility
- `aria-label` on close button
- Semantic HTML (header, main, etc.)
- Keyboard navigation (ESC to close modal)

### Code Organization
- Feature-based structure (api, hooks, components)
- Reusable components (KpiCard)
- Custom hooks for data fetching
- Separation of concerns

### Error Boundaries (Future Enhancement)
- Could add Error Boundary component
- Catches React errors in component tree
- Shows fallback UI

### TypeScript (Future Enhancement)
- Could migrate to TypeScript
- Type safety for props and API responses
- Better IDE autocomplete

---

## 📝 Quick Reference

### React Query Hook Pattern
```javascript
const { data, isLoading, error } = useQuery({
  queryKey: ['key'],
  queryFn: ({ signal }) => apiFunction(signal),
  staleTime: 60 * 1000,
});
```

### useState Pattern
```javascript
const [value, setValue] = useState(initialValue);
```

### useEffect Pattern
```javascript
useEffect(() => {
  // effect
  return () => {
    // cleanup
  };
}, [dependencies]);
```

### useMemo Pattern
```javascript
const memoized = useMemo(() => {
  return expensiveComputation();
}, [dependencies]);
```

### SSE Pattern
```javascript
const eventSource = new EventSource(url);
eventSource.onmessage = (event) => {
  const data = JSON.parse(event.data);
  // handle data
};
eventSource.close(); // cleanup
```

---

**Good luck with your interview! 🚀**

Remember:
- Speak confidently about the code
- Explain the "why" behind decisions
- Mention trade-offs and alternatives
- Show understanding of React patterns
- Demonstrate knowledge of React Query
- Be ready to discuss performance optimizations

