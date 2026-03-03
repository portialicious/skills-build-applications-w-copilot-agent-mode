# Build Applications with GitHub Copilot Agent Mode

<img src="https://octodex.github.com/images/Professortocat_v2.png" align="right" height="200px" />

Hey portialicious!

Mona here. I'm done preparing your exercise. Hope you enjoy! 💚

Remember, it's self-paced so feel free to take a break! ☕️

[![](https://img.shields.io/badge/Go%20to%20Exercise-%E2%86%92-1f883d?style=for-the-badge&logo=github&labelColor=197935)](https://github.com/portialicious/skills-build-applications-w-copilot-agent-mode/issues/1)

---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

import React, { useState, useEffect } from 'react';
import axios from 'axios';
import './Dashboard.css';

const Dashboard = ({ userId }) => {
  const [userProfile, setUserProfile] = useState(null);
  const [activities, setActivities] = useState([]);
  const [loading, setLoading] = useState(true);
  const [newActivity, setNewActivity] = useState({
    activity_type: 'running',
    duration_minutes: 30,
    distance_km: 5,
    intensity: 'moderate'
  });

  useEffect(() => {
    fetchUserProfile();
    fetchActivities();
  }, [userId]);

  const fetchUserProfile = async () => {
    try {
      const response = await axios.get(`http://localhost:8000/api/profiles/${userId}/`);
      setUserProfile(response.data);
    } catch (error) {
      console.error('Error fetching profile:', error);
    }
  };

  const fetchActivities = async () => {
    try {
      const response = await axios.get(`http://localhost:8000/api/activities/?user=${userId}`);
      setActivities(response.data);
    } catch (error) {
      console.error('Error fetching activities:', error);
    } finally {
      setLoading(false);
    }
  };

  const handleLogActivity = async (e) => {
    e.preventDefault();
    try {
      await axios.post(`http://localhost:8000/api/profiles/${userId}/log_activity/`, newActivity);
      setNewActivity({
        activity_type: 'running',
        duration_minutes: 30,
        distance_km: 5,
        intensity: 'moderate'
      });
      fetchUserProfile();
      fetchActivities();
    } catch (error) {
      console.error('Error logging activity:', error);
    }
  };

  if (loading) return <div>Loading...</div>;

  return (
    <div className="dashboard">
      <div className="header">
        <h1>OctoFit Tracker</h1>
        {userProfile && <div className="points-display">{userProfile.points} Points</div>}
      </div>

      <div className="form-section">
        <form onSubmit={handleLogActivity}>
          <h2>Log Activity</h2>
          <select
            value={newActivity.activity_type}
            onChange={(e) => setNewActivity({...newActivity, activity_type: e.target.value})}
          >
            <option value="running">Running</option>
            <option value="walking">Walking</option>
            <option value="strength">Strength Training</option>
          </select>
          <input
            type="number"
            placeholder="Duration (minutes)"
            value={newActivity.duration_minutes}
            onChange={(e) => setNewActivity({...newActivity, duration_minutes: parseInt(e.target.value)})}
          />
          <input
            type="number"
            placeholder="Distance (km)"
            value={newActivity.distance_km}
            onChange={(e) => setNewActivity({...newActivity, distance_km: parseFloat(e.target.value)})}
          />
          <select
            value={newActivity.intensity}
            onChange={(e) => setNewActivity({...newActivity, intensity: e.target.value})}
          >
            <option value="low">Low</option>
            <option value="moderate">Moderate</option>
            <option value="high">High</option>
          </select>
          <button type="submit">Log Activity</button>
        </form>
      </div>

      <div className="activities-section">
        <h2>Recent Activities</h2>
        {activities.map((activity) => (
          <div key={activity.id} className="activity-card">
            <div className="activity-type">{activity.activity_type}</div>
            <div className="activity-details">
              <p>{activity.duration_minutes} minutes - {activity.intensity} intensity</p>
              <p className="points">+{activity.points_earned} points</p>
            </div>
          </div>
        ))}
      </div>
    </div>
  );
};

export default Dashboard;
