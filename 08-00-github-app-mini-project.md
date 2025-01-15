# **GitHub Card List App Tutorial**

---

## **1. GitHub Card List App**

The goal of this app is to display a list of GitHub users, with each user’s data displayed in a card format. The app will also allow searching for users by their usernames. The user information will be fetched from the GitHub API.

### **Features of the App:**
- Display a list of GitHub user profiles.
- Search functionality to filter users by username.
- Modular components (Card, CardList, etc.) for better maintainability.

---

## **2. Create React App**

First, we need to create a React application using the `create-react-app` tool.

### **Steps:**

1. Open your terminal and navigate to the directory where you want to create the app.
2. Run the following command to create the app:

```bash
npx create-react-app github-card-list
```

3. Navigate into the newly created directory:

```bash
cd github-card-list
```

4. Start the development server:

```bash
npm start
```

This will open your app in the browser at `http://localhost:3000/`.

---

## **3. Add Components**

In this app, we will break down the UI into smaller components:

- `App`: The main component that holds everything.
- `CardList`: This component will render the list of user cards.
- `Card`: This component will display individual user profile information.

Create a folder named `components` inside the `src` folder to store our components.

```bash
mkdir src/components
```

---

## **4. Implement Card Component**

The `Card` component will display individual user profile information, such as the username, avatar, and profile link.

### **Card Component (src/components/Card.js)**

```jsx
import React from 'react';

function Card({ profile }) {
  return (
    <div className="card">
      <img src={profile.avatar_url} alt={profile.login} />
      <h2>{profile.login}</h2>
      <a href={profile.html_url} target="_blank" rel="noopener noreferrer">
        View Profile
      </a>
    </div>
  );
}

export default Card;
```

### **Explanation:**
- **`profile` prop**: The `Card` component receives a `profile` object as a prop. This object contains the user's GitHub data.
- **Rendering User Data**: We render the user's avatar (`avatar_url`), username (`login`), and a link to the user's GitHub profile (`html_url`).

---

## **5. Pass the Profile Data from `CardList` to `Card` Component**

The `CardList` component will receive the list of profiles and pass each profile to the `Card` component.

### **CardList Component (src/components/CardList.js)**

```jsx
import React from 'react';
import Card from './Card';

function CardList({ profiles }) {
  return (
    <div className="card-list">
      {profiles.map((profile) => (
        <Card key={profile.id} profile={profile} />
      ))}
    </div>
  );
}

export default CardList;
```

### **Explanation:**
- **`profiles` prop**: The `CardList` component receives an array of profiles as a prop and maps over it.
- **`map()` method**: We loop through each profile and pass it to the `Card` component.

---

## **6. Looping through an Array to Display Cards**

To display multiple cards, we need an array of user profile data. We can fetch this data from GitHub using their API. We'll simulate the array for now and add the API call later.

### **Example of Profile Data Array:**

```jsx
const profiles = [
  {
    id: 1,
    login: 'octocat',
    avatar_url: 'https://avatars.githubusercontent.com/u/583231?v=4',
    html_url: 'https://github.com/octocat'
  },
  {
    id: 2,
    login: 'mojombo',
    avatar_url: 'https://avatars.githubusercontent.com/u/1?v=4',
    html_url: 'https://github.com/mojombo'
  }
  // Add more profiles here
];
```

We will pass this `profiles` array to the `CardList` component to display multiple cards.

---

## **7. Adding a Search Form**

We want to allow the user to search for GitHub profiles. To do this, we'll add a search form with an input box.

### **Search Form (in App Component)**

```jsx
import React, { useState } from 'react';

function SearchForm({ onSearch }) {
  const [query, setQuery] = useState('');

  const handleChange = (event) => {
    setQuery(event.target.value);
  };

  const handleSubmit = (event) => {
    event.preventDefault();
    onSearch(query);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={query}
        onChange={handleChange}
        placeholder="Search GitHub users"
      />
      <button type="submit">Search</button>
    </form>
  );
}

export default SearchForm;
```

### **Explanation:**
- **`useState` hook**: We use the `useState` hook to manage the query input by the user.
- **`onSearch` callback**: This function is called when the form is submitted. It sends the search query to the parent component (`App`).

---

## **8. Using `useState` or `useRef` to Get Input Box Value**

In the previous step, we used the `useState` hook to store and manage the value of the search input. We could alternatively use `useRef` to get the value, but using `useState` is the more common approach in React for form handling.

---

## **9. Passing Data to the App Component for Search**

Now, in the `App` component, we will handle the state for storing profiles and search queries.

### **App Component (src/App.js)**

```jsx
import React, { useState, useEffect } from 'react';
import SearchForm from './components/SearchForm';
import CardList from './components/CardList';

function App() {
  const [profiles, setProfiles] = useState([]);
  const [filteredProfiles, setFilteredProfiles] = useState([]);

  const fetchProfiles = async () => {
    const response = await fetch('https://api.github.com/users/octocat');
    const data = await response.json();
    setProfiles([data]); // Assume we fetch a single profile for now
    setFilteredProfiles([data]);
  };

  const handleSearch = (query) => {
    const filtered = profiles.filter((profile) =>
      profile.login.toLowerCase().includes(query.toLowerCase())
    );
    setFilteredProfiles(filtered);
  };

  useEffect(() => {
    fetchProfiles();
  }, []);

  return (
    <div>
      <SearchForm onSearch={handleSearch} />
      <CardList profiles={filteredProfiles} />
    </div>
  );
}

export default App;
```

### **Explanation:**
- **`fetchProfiles` function**: This function fetches profile data from the GitHub API. We're simulating it with just one profile for simplicity.
- **`handleSearch` function**: This function filters the profiles array based on the query.
- **`useEffect` hook**: It fetches the profiles when the component mounts (initial load).

---

## **10. Filtering the Profiles Array**

When the user types a query in the search form, the `handleSearch` function is called. It filters the `profiles` array to match the query and updates the `filteredProfiles` array that is passed to the `CardList` component.

---

## **11. Assignment**

1. **Add More Profiles**: Fetch a list of profiles instead of just one, and display multiple profiles in the `CardList` component.
2. **Enhance Search**: Allow users to search not only by username but also by other profile attributes, such as full name or bio.
3. **Styling**: Style the components to make the app look visually appealing. Use CSS or a library like `styled-components` or `CSS modules`.
4. **Error Handling**: Add error handling for when the GitHub API call fails.

---
