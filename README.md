# ai-agent
ai agent for financial advisors
// src/AdvisorDashboard.js
import React, { useEffect, useState } from "react";

// Mock functions for data fetching and AI logic
const fetchCrmClients = async () => [
  { name: "Jane Doe", dob: "1984-07-27", portfolioReturn: -0.08, retirementDate: "2030-01-01" },
  { name: "John Smith", dob: "1960-02-10", portfolioReturn: 0.02, retirementDate: "2025-01-01" },
  { name: "Alice Lee", dob: "1975-07-27", portfolioReturn: 0.06, retirementDate: "2040-01-01" },
];

const fetchCrmTasks = async () => [
  { task: "Call Alice Lee (birthday follow-up)", due: "Today" },
  { task: "Prepare proposal for Jane Doe", due: "Tomorrow" },
  { task: "Schedule Q3 review with John Smith", due: "Next Week" },
];

const fetchRelevantArticles = async () => [
  { title: "How Markets Affect Retirees", url: "https://news.example.com/retirees" },
  { title: "Fed Rate Hike and Your Portfolio", url: "https://news.example.com/fed-rate" },
];

// Simple rules for demo; replace with GPT/LLM logic as needed
function detectAlerts(clients) {
  const today = new Date();
  return clients
    .map((c) => {
      // Portfolio underperformance
      if (c.portfolioReturn < -0.05) {
        return { name: c.name, alert: "Portfolio underperforming (down 8% vs. S&P 500)" };
      }
      // Upcoming retirement (within 12 months)
      const retirement = new Date(c.retirementDate);
      const monthsToRetirement =
        (retirement.getFullYear() - today.getFullYear()) * 12 +
        (retirement.getMonth() - today.getMonth());
      if (monthsToRetirement > 0 && monthsToRetirement <= 12) {
        return { name: c.name, alert: `Upcoming retirement in ${monthsToRetirement} months` };
      }
      // Recent birthday (within last 2 days)
      const dob = new Date(c.dob);
      dob.setFullYear(today.getFullYear());
      const diffDays = Math.abs((today - dob) / (1000 * 60 * 60 * 24));
      if (diffDays <= 1) {
        return { name: c.name, alert: "Birthday yesterday" };
      }
      return null;
    })
    .filter(Boolean);
}

function generateTalkingPoints(alerts) {
  // Basic mapping for demo; replace with GPT for more natural output
  return alerts.map((a) => {
    if (a.alert.includes("portfolio")) return `Discuss rebalancing portfolio with ${a.name}`;
    if (a.alert.includes("retirement")) return `Review annuity options for ${a.name} ahead of retirement`;
    if (a.alert.includes("Birthday")) return `Send birthday greeting to ${a.name}`;
    return `Check in with ${a.name}`;
  });
}

export default function AdvisorDashboard() {
  const [alerts, setAlerts] = useState([]);
  const [talkingPoints, setTalkingPoints] = useState([]);
  const [tasks, setTasks] = useState([]);
  const [articles, setArticles] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    async function fetchData() {
      const clients = await fetchCrmClients();
      const tasks = await fetchCrmTasks();
      const articles = await fetchRelevantArticles();
      const alerts = detectAlerts(clients);
      const talkingPoints = generateTalkingPoints(alerts);
      setAlerts(alerts);
      setTalkingPoints(talkingPoints);
      setTasks(tasks);
      setArticles(articles);
      setLoading(false);
    }
    fetchData();
  }, []);

  if (loading) return <div style={{ padding: 40, fontSize: 18 }}>Loading...</div>;

  return (
    <div style={{ fontFamily: "Inter, sans-serif", background: "#F5F6FA", minHeight: "100vh" }}>
      {/* Top Bar */}
      <div style={{
        background: "#fff",
        height: 64,
        display: "flex",
        alignItems: "center",
        justifyContent: "space-between",
        padding: "0 32px",
        boxShadow: "0 2px 8px rgba(0,0,0,0.04)",
        marginBottom: 40
      }}>
        <span style={{ fontWeight: 700, fontSize: 20, color: "#2E6BFF" }}>AdvisorAI</span>
        <span style={{ fontWeight: 500, fontSize: 18 }}>Daily Digest</span>
        <span>
          {new Date().toLocaleDateString(undefined, { year: "numeric", month: "short", day: "numeric" })}
          <span style={{
            width: 40,
            height: 40,
            marginLeft: 16,
            display: "inline-block",
            borderRadius: "50%",
            background: "#d2e3fc",
            verticalAlign: "middle"
          }} />
        </span>
      </div>

      {/* Main Content */}
      <div style={{
        display: "flex",
        gap: 32,
        justifyContent: "center",
        alignItems: "flex-start",
        padding: "0 32px"
      }}>
        {/* Card 1: Clients Needing Attention */}
        <div style={{
          background: "#fff",
          borderRadius: 12,
          boxShadow: "0 2px 8px rgba(0,0,0,0.04)",
          padding: 32,
          minWidth: 320,
          flex: 1
        }}>
          <div style={{ fontWeight: 700, fontSize: 16, marginBottom: 20 }}>🔔 Clients Needing Attention</div>
          {alerts.length === 0 ? <div>No urgent alerts today!</div> : alerts.map((a, idx) => (
            <div key={idx} style={{ marginBottom: 18 }}>
              <div style={{ fontWeight: 600 }}>{a.name}</div>
              <div style={{ fontSize: 14, color: "#444", marginBottom: 8 }}>{a.alert}</div>
              <div>
                <button style={btnStyle}>View Portfolio</button>
                <button style={btnStyleAlt}>Message</button>
              </div>
            </div>
          ))}
        </div>

        {/* Card 2: Suggested Talking Points & Content */}
        <div style={{
          background: "#fff",
          borderRadius: 12,
          boxShadow: "0 2px 8px rgba(0,0,0,0.04)",
          padding: 32,
          minWidth: 320,
          flex: 1
        }}>
          <div style={{ fontWeight: 700, fontSize: 16, marginBottom: 20 }}>💬 Suggested Talking Points</div>
          <ul style={{ marginBottom: 16 }}>
            {talkingPoints.map((point, idx) => (
              <li key={idx} style={{ marginBottom: 8 }}>{point}</li>
            ))}
          </ul>
          <div style={{ borderTop: "1px solid #eee", margin: "16px 0" }} />
          <div style={{ fontWeight: 600, marginBottom: 10 }}>Recommended Articles:</div>
          {articles.map((a, idx) => (
            <div key={idx} style={{ marginBottom: 8 }}>
              <a href={a.url} target="_blank" rel="noopener noreferrer" style={{ color: "#2E6BFF" }}>
                📄 {a.title}
              </a>
            </div>
          ))}
          <button style={{ ...btnStyle, marginTop: 12 }}>Share Article</button>
        </div>

        {/* Card 3: CRM Tasks */}
        <div style={{
          background: "#fff",
          borderRadius: 12,
          boxShadow: "0 2px 8px rgba(0,0,0,0.04)",
          padding: 32,
          minWidth: 320,
          flex: 1
        }}>
          <div style={{ fontWeight: 700, fontSize: 16, marginBottom: 20 }}>📋 CRM Tasks</div>
          <ul>
            {tasks.map((t, idx) => (
              <li key={idx} style={{ marginBottom: 12 }}>
                <input type="checkbox" id={`task${idx}`} style={{ marginRight: 8 }} />
                <label htmlFor={`task${idx}`}>{t.task} <span style={{ color: "#8C94A6" }}>— Due: {t.due}</span></label>
              </li>
            ))}
          </ul>
          <button style={btnStyle}>Add Task</button>
        </div>
      </div>
    </div>
  );
}

// Button styles
const btnStyle = {
  background: "#2E6BFF",
  color: "#fff",
  border: "none",
  borderRadius: 6,
  padding: "6px 16px",
  fontWeight: 600,
  marginRight: 10,
  cursor: "pointer",
};

const btnStyleAlt = {
  ...btnStyle,
  background: "#E9F1FE",
  color: "#2E6BFF",
  border: "1px solid #B5D1F8",
};

// Usage: import AdvisorDashboard from "./AdvisorDashboard";
// Then render <AdvisorDashboard /> in your App.js
