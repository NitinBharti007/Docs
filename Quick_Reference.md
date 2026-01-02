# Quick Reference Cheat Sheet for Interview

## 🚀 Project Overview
- **What**: React dashboard for advertising campaign performance
- **Tech**: React 18 + Vite + TanStack React Query + SSE
- **Purpose**: Monitor campaigns, view KPIs, real-time updates

---

## 📦 Key Technologies

### TanStack React Query
- **What**: Data fetching, caching, synchronization library
- **Why**: Automatic caching, loading states, error handling
- **Key Features**:
  - `useQuery`: Fetches and caches data
  - `QueryClient`: Manages cache
  - `queryKey`: Unique identifier for cache
  - `staleTime`: How long data stays fresh
  - `invalidateQueries`: Force refetch

### Server-Sent Events (SSE)
- **What**: One-way server-to-client streaming
- **Why**: Real-time updates without polling
- **How**: `EventSource` API connects to `/stream` endpoint

### React Hooks Used
- `useState`: Component state
- `useEffect`: Side effects (API, subscriptions)
- `useMemo`: Memoized computed values
- `useCallback`: Memoized functions
- `useRef`: Mutable reference (doesn't trigger re-render)

---

## 🗂️ File Structure

```
src/
├── api/
│   ├── client.js          # HTTP client with rate limiting
│   └── campaigns.js      # API endpoint functions
├── hooks/
│   ├── useCampaigns.js           # Fetch all campaigns
│   ├── useAggregateInsights.js   # Fetch aggregate KPIs
│   ├── useCampaignDetails.js     # Fetch single campaign
│   └── useCampaignInsights.js    # Fetch campaign insights
├── components/
│   ├── AggregateKpis.jsx      # Top KPI cards
│   ├── CampaignFilters.jsx     # Search & filters
│   ├── CampaignList.jsx        # Campaign list
│   ├── CampaignModal.jsx       # Detail modal
│   ├── KpiCard.jsx             # Reusable KPI card
│   └── LiveStream.jsx          # SSE handler
├── App.jsx                     # Main component
├── main.jsx                    # Entry point
└── styles.css                  # Global styles
```

---

## 🔑 Key Concepts

### 1. Rate Limiting Handling
- **Problem**: API limits 10 requests/minute
- **Solution**: 
  - Detect 429 status
  - Read `retry_after` from response
  - Wait and retry (max 2 times)
- **Location**: `src/api/client.js`

### 2. Request Cancellation
- **How**: AbortController signal passed to fetch
- **Why**: Cancel when component unmounts or query disabled
- **Location**: All hooks pass `signal` to API functions

### 3. Filtering & Sorting
- **How**: `useMemo` recalculates when dependencies change
- **Filters**: Search, Status, Platform
- **Sorts**: Newest, Budget, Daily Budget
- **Location**: `src/App.jsx` lines 30-60

### 4. Live Mode (SSE)
- **How**: EventSource connects when enabled
- **Flow**: Server → EventSource → onUpdate → State → UI
- **Reconnection**: Max 2 attempts on error
- **Location**: `src/components/LiveStream.jsx`

---

## 💡 Common Interview Questions

### Q: Why React Query?
**A**: Automatic caching, loading states, error handling, request deduplication, background refetching, request cancellation.

### Q: How does rate limiting work?
**A**: 
1. API returns 429 with `retry_after`
2. Client waits specified time
3. Retries (max 2 times)
4. Throws error if still fails

### Q: Why useMemo for filtering?
**A**: Prevents expensive recalculation on every render. Only recalculates when dependencies (campaigns, filters) change.

### Q: How does SSE work?
**A**: 
1. User enables Live Mode
2. EventSource connects to `/stream`
3. Server sends JSON events
4. Component parses and updates state
5. UI reflects changes immediately

### Q: What's the difference between isLoading and isFetching?
**A**: 
- `isLoading`: Only true on initial fetch (no cache)
- `isFetching`: True on any fetch (including background)

### Q: How do you handle errors?
**A**: 
1. HTTP client throws `HttpError` with status
2. React Query catches, provides in hook result
3. Components check `error` prop, show error UI
4. Retry logic: 429 = 2 retries, others = 3 retries

---

## 🎯 Component Responsibilities

| Component | Responsibility |
|-----------|---------------|
| `App` | Main layout, state management, data fetching |
| `AggregateKpis` | Display 9 aggregate KPI cards |
| `CampaignFilters` | Search, status, platform filters, sorting |
| `CampaignList` | Display list of campaigns, handle selection |
| `CampaignModal` | Show campaign details, insights, live mode |
| `KpiCard` | Reusable card for displaying KPIs |
| `LiveStream` | Handle SSE connection for real-time updates |

---

## 🔄 Data Flow

```
1. App Loads
   ↓
2. React Query Hooks Fetch Data
   ├── useCampaigns() → GET /campaigns
   └── useAggregateInsights() → GET /campaigns/insights
   ↓
3. Data Cached by React Query
   ↓
4. User Interacts
   ├── Filters/Searches → useMemo recalculates
   ├── Clicks Campaign → Sets selectedCampaignId
   └── Opens Modal → Fetches details & insights
   ↓
5. Real-time Updates (if Live Mode)
   └── SSE Stream → Updates insights
```

---

## 📊 React Query Hook Pattern

```javascript
const { data, isLoading, error } = useQuery({
  queryKey: ['campaigns'],              // Cache key
  queryFn: ({ signal }) => apiFn(signal), // Fetch function
  staleTime: 60 * 1000,                 // 60 seconds
  enabled: !!id,                         // Conditional fetch
  retry: (count, error) => count < 3,   // Retry logic
});
```

---

## 🎨 State Management

### App Component State
- `selectedCampaignId`: Which campaign is selected
- `searchTerm`: Search input value
- `statusFilter`: Status filter value
- `platformFilter`: Platform filter value
- `sortBy`: Sort option
- `lastRefresh`: Timestamp of last refresh

### Modal Component State
- `liveMode`: Whether Live Mode is enabled
- `liveInsights`: Real-time insights from SSE

---

## 🛠️ API Endpoints

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/campaigns` | GET | List all campaigns |
| `/campaigns/{id}` | GET | Get single campaign |
| `/campaigns/insights` | GET | Aggregate insights |
| `/campaigns/{id}/insights` | GET | Campaign insights |
| `/campaigns/{id}/insights/stream` | SSE | Real-time stream |

---

## ⚡ Performance Optimizations

1. **useMemo**: Memoizes filtered/sorted campaigns
2. **useCallback**: Memoizes callback functions
3. **React Query Caching**: Reduces API calls
4. **Request Cancellation**: Prevents unnecessary requests
5. **Stale Time**: Prevents unnecessary refetches

---

## 🐛 Error Handling

1. **HTTP Errors**: Custom `HttpError` class with status
2. **429 Rate Limit**: Automatic retry with delay
3. **Network Errors**: Generic retry (max 3 times)
4. **Component Errors**: Error UI with retry button
5. **SSE Errors**: Reconnection attempts (max 2)

---

## 🔐 Key Code Locations

| Feature | File | Lines |
|---------|------|-------|
| Rate Limiting | `src/api/client.js` | 31-36 |
| Request Cancellation | `src/api/client.js` | 21 |
| Filtering/Sorting | `src/App.jsx` | 30-60 |
| SSE Connection | `src/components/LiveStream.jsx` | 19-54 |
| React Query Config | `src/main.jsx` | 7-19 |
| Modal Behavior | `src/components/CampaignModal.jsx` | 15-31 |

---

## 📝 Quick Code Snippets

### useState
```javascript
const [value, setValue] = useState(initialValue);
```

### useEffect
```javascript
useEffect(() => {
  // effect
  return () => {
    // cleanup
  };
}, [dependencies]);
```

### useMemo
```javascript
const memoized = useMemo(() => {
  return expensiveComputation();
}, [dependencies]);
```

### useQuery
```javascript
const { data, isLoading, error } = useQuery({
  queryKey: ['key'],
  queryFn: () => fetchData(),
});
```

### EventSource (SSE)
```javascript
const eventSource = new EventSource(url);
eventSource.onmessage = (event) => {
  const data = JSON.parse(event.data);
  // handle data
};
eventSource.close();
```

---

## 🎓 Interview Tips

1. **Start with Overview**: Explain what the project does
2. **Explain Architecture**: Component structure, data flow
3. **Highlight Key Features**: Rate limiting, SSE, caching
4. **Discuss Trade-offs**: Why React Query? Why SSE vs WebSockets?
5. **Show Understanding**: Explain hooks, memoization, performance
6. **Be Honest**: If you don't know something, say so

---

**Good luck! 🚀**

