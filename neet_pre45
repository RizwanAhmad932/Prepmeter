// File: src/firebase-config.js
import { initializeApp } from "firebase/app";
import { getFirestore } from "firebase/firestore";
import { getAuth } from "firebase/auth";

const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT_ID.appspot.com",
  messagingSenderId: "SENDER_ID",
  appId: "APP_ID"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);
const auth = getAuth(app);

export { db, auth };

// File: src/App.js
import React, { useEffect, useState } from 'react';
import { onAuthStateChanged } from 'firebase/auth';
import { auth } from './firebase-config';
import LoginPage from './pages/LoginPage';
import RegistrationPage from './pages/RegistrationPage';
import DashboardPage from './pages/DashboardPage';

export const isAdmin = (uid) => {
  const adminUIDs = ['YOUR_ADMIN_UID']; // Replace with actual admin UID
  return adminUIDs.includes(uid);
};

export default function App() {
  const [user, setUser] = useState(null);
  const [currentPage, setCurrentPage] = useState('login');

  useEffect(() => {
    onAuthStateChanged(auth, (user) => {
      if (user) {
        setUser(user);
        setCurrentPage('dashboard');
      } else {
        setUser(null);
      }
    });
  }, []);

  const renderPage = () => {
    if (currentPage === 'login') return <LoginPage setCurrentPage={setCurrentPage} />;
    if (currentPage === 'register') return <RegistrationPage setCurrentPage={setCurrentPage} />;
    if (currentPage === 'dashboard') return <DashboardPage user={user} setCurrentPage={setCurrentPage} />;
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-indigo-100 to-pink-100 p-4">
      {renderPage()}
    </div>
  );
}

// File: src/pages/LoginPage.js
import React, { useState } from 'react';
import { signInWithEmailAndPassword } from 'firebase/auth';
import { auth } from '../firebase-config';

export default function LoginPage({ setCurrentPage }) {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [message, setMessage] = useState('');

  const handleLogin = async () => {
    try {
      await signInWithEmailAndPassword(auth, email, password);
      setMessage("Login successful!");
    } catch (error) {
      setMessage("Login failed. Check credentials.");
    }
  };

  return (
    <div className="max-w-md mx-auto bg-white rounded-lg shadow p-6">
      <h2 className="text-xl font-bold mb-4">Login</h2>
      <input value={email} onChange={(e) => setEmail(e.target.value)} placeholder="Email" className="mb-2 w-full border p-2" />
      <input type="password" value={password} onChange={(e) => setPassword(e.target.value)} placeholder="Password" className="mb-4 w-full border p-2" />
      <button onClick={handleLogin} className="bg-indigo-500 text-white w-full py-2 rounded">Log in</button>
      {message && <p className="mt-4 text-sm text-red-500">{message}</p>}
      <p className="mt-2 text-sm">Don't have an account? <button onClick={() => setCurrentPage('register')} className="text-blue-600 underline">Register</button></p>
    </div>
  );
}

// File: src/pages/RegistrationPage.js
import React, { useState } from 'react';
import { createUserWithEmailAndPassword } from 'firebase/auth';
import { setDoc, doc } from 'firebase/firestore';
import { auth, db } from '../firebase-config';

export default function RegistrationPage({ setCurrentPage }) {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [name, setName] = useState('');
  const [message, setMessage] = useState('');

  const handleRegister = async () => {
    try {
      const res = await createUserWithEmailAndPassword(auth, email, password);
      await setDoc(doc(db, "users", res.user.uid), {
        name,
        email,
        subscription: "none"
      });
      setMessage("Registration successful! Please login.");
      setCurrentPage('login');
    } catch (error) {
      setMessage("Registration failed.");
    }
  };

  return (
    <div className="max-w-md mx-auto bg-white rounded-lg shadow p-6">
      <h2 className="text-xl font-bold mb-4">Register</h2>
      <input value={name} onChange={(e) => setName(e.target.value)} placeholder="Name" className="mb-2 w-full border p-2" />
      <input value={email} onChange={(e) => setEmail(e.target.value)} placeholder="Email" className="mb-2 w-full border p-2" />
      <input type="password" value={password} onChange={(e) => setPassword(e.target.value)} placeholder="Password" className="mb-4 w-full border p-2" />
      <button onClick={handleRegister} className="bg-green-500 text-white w-full py-2 rounded">Register</button>
      {message && <p className="mt-4 text-sm text-red-500">{message}</p>}
    </div>
  );
}

// File: src/pages/DashboardPage.js
import React from 'react';
import { signOut } from 'firebase/auth';
import { auth } from '../firebase-config';
import { isAdmin } from '../App';

export default function DashboardPage({ user, setCurrentPage }) {
  return (
    <div className="max-w-2xl mx-auto bg-white rounded-lg shadow p-6">
      <h2 className="text-2xl font-bold mb-4">Welcome, {user?.email}</h2>
      <p className="mb-4">UID: {user?.uid}</p>
      {isAdmin(user?.uid) && (
        <div className="mb-4 p-4 border rounded bg-yellow-100">You are an <strong>Admin</strong></div>
      )}
      <button onClick={() => signOut(auth)} className="bg-red-500 text-white py-2 px-4 rounded">Logout</button>
    </div>
  );
}
