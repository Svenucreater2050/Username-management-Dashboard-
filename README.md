user-management-dashboard/
│
├── public/
│   └── index.html
│
├── src/
│   ├── components/
│   │   ├── UserList.js         # Displays the list of users
│   │   ├── UserForm.js         # Handles Add/Edit form
│   │   ├── UserManagement.js   # Combines UserList & UserForm
│   │
│   ├── utils/
│   │   └── api.js              # Axios instance setup
│   │
│   ├── App.js                  # Main application file
│   ├── index.css               # Stylesheet
│   ├── index.js                # ReactDOM render entry point
│
├── package.json
├── README.md
└── .gitignore








src/utils/api.js


import axios from 'axios';

// Axios instance for JSONPlaceholder API
const api = axios.create({
  baseURL: 'https://jsonplaceholder.typicode.com',
  headers: { 'Content-Type': 'application/json' },
});

export default api;


src/components/UserManagement.js


import React, { useState, useEffect } from 'react';
import api from '../utils/api';
import UserList from './UserList';
import UserForm from './UserForm';

const UserManagement = () => {
  const [users, setUsers] = useState([]);
  const [selectedUser, setSelectedUser] = useState(null);
  const [error, setError] = useState('');

  // Fetch users on component mount
  useEffect(() => {
    const fetchUsers = async () => {
      try {
        const response = await api.get('/users');
        setUsers(response.data);
      } catch (err) {
        setError('Failed to fetch users. Please try again later.');
      }
    };
    fetchUsers();
  }, []);

  // Add new user
  const handleAddUser = async (user) => {
    try {
      const response = await api.post('/users', user);
      setUsers([...users, response.data]);
    } catch {
      setError('Failed to add user.');
    }
  };

  // Edit existing user
  const handleEditUser = async (user) => {
    try {
      await api.put(`/users/${user.id}`, user);
      setUsers(users.map((u) => (u.id === user.id ? user : u)));
    } catch {
      setError('Failed to edit user.');
    }
  };

  // Delete user
  const handleDeleteUser = async (userId) => {
    try {
      await api.delete(`/users/${userId}`);
      setUsers(users.filter((u) => u.id !== userId));
    } catch {
      setError('Failed to delete user.');
    }
  };

  return (
    <div>
      <h1>User Management Dashboard</h1>
      {error && <p style={{ color: 'red' }}>{error}</p>}
      <UserList
        users={users}
        onEdit={setSelectedUser}
        onDelete={handleDeleteUser}
      />
      <UserForm
        user={selectedUser}
        onSave={(user) => {
          if (user.id) handleEditUser(user);
          else handleAddUser(user);
        }}
        onCancel={() => setSelectedUser(null)}
      />
    </div>
  );
};

export default UserManagement;




src/components/UserList.js


import React from 'react';

const UserList = ({ users, onEdit, onDelete }) => (
  <div>
    <h2>Users</h2>
    <table border="1">
      <thead>
        <tr>
          <th>ID</th>
          <th>First Name</th>
          <th>Last Name</th>
          <th>Email</th>
          <th>Department</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        {users.map((user) => (
          <tr key={user.id}>
            <td>{user.id}</td>
            <td>{user.firstName}</td>
            <td>{user.lastName}</td>
            <td>{user.email}</td>
            <td>{user.department}</td>
            <td>
              <button onClick={() => onEdit(user)}>Edit</button>
              <button onClick={() => onDelete(user.id)}>Delete</button>
            </td>
          </tr>
        ))}
      </tbody>
    </table>
  </div>
);

export default UserList;




src/components/UserForm.js



import React, { useState, useEffect } from 'react';

const UserForm = ({ user, onSave, onCancel }) => {
  const [formState, setFormState] = useState({
    id: '',
    firstName: '',
    lastName: '',
    email: '',
    department: '',
  });

  useEffect(() => {
    if (user) setFormState(user);
  }, [user]);

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormState((prev) => ({ ...prev, [name]: value }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    onSave(formState);
    setFormState({ id: '', firstName: '', lastName: '', email: '', department: '' });
  };

  return (
    <div>
      <h2>{user ? 'Edit User' : 'Add User'}</h2>
      <form onSubmit={handleSubmit}>
        <input
          name="firstName"
          value={formState.firstName}
          onChange={handleChange}
          placeholder="First Name"
          required
        />
        <input
          name="lastName"
          value={formState.lastName}
          onChange={handleChange}
          placeholder="Last Name"
          required
        />
        <input
          name="email"
          value={formState.email}
          onChange={handleChange}
          placeholder="Email"
          type="email"
          required
        />
        <input
          name="department"
          value={formState.department}
          onChange={handleChange}
          placeholder="Department"
          required
        />
        <button type="submit">Save</button>
        <button type="button" onClick={onCancel}>
          Cancel
        </button>
      </form>
    </div>
  );
};

export default UserForm;



src/App.js




import React from 'react';
import UserManagement from './components/UserManagement';

const App = () => <UserManagement />;

export default App;





