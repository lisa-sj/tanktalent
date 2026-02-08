import { useState, useEffect, useMemo } from "react";
import { LineChart, Line, XAxis, YAxis, Tooltip, ResponsiveContainer, RadarChart, Radar, PolarGrid, PolarAngleAxis, PolarRadiusAxis, BarChart, Bar, Cell } from "recharts";

const COLORS = {
  bg: "#0B0F1A",
  surface: "#111827",
  surfaceLight: "#1F2937",
  border: "#2D3748",
  borderLight: "#374151",
  primary: "#3B82F6",
  primaryDim: "#1E40AF",
  accent: "#10B981",
  accentDim: "#065F46",
  warning: "#F59E0B",
  danger: "#EF4444",
  text: "#F9FAFB",
  textMuted: "#9CA3AF",
  textDim: "#6B7280",
  gold: "#FBBF24",
  purple: "#8B5CF6",
  pink: "#EC4899",
};

const SKILLS_DB = [
  "Python", "JavaScript", "TypeScript", "React", "Node.js", "AWS", "Docker", "Kubernetes",
  "PostgreSQL", "MongoDB", "GraphQL", "REST API", "Machine Learning", "TensorFlow", "PyTorch",
  "Java", "Go", "Rust", "C++", "SQL", "Redis", "Elasticsearch", "CI/CD", "Terraform",
  "System Design", "Microservices", "Agile", "Leadership", "Communication", "Problem Solving"
];

const COMPANIES = ["Google", "Meta", "Amazon", "Apple", "Microsoft", "Netflix", "Stripe", "Airbnb", "Uber", "Salesforce"];

function generateCandidates(count = 50) {
  const firstNames = ["Alex", "Jordan", "Morgan", "Taylor", "Casey", "Riley", "Quinn", "Avery", "Sage", "Rowan", "Cameron", "Dakota", "Reese", "Skyler", "Jamie", "Drew", "Blake", "Emery", "Finley", "Harper", "Kai", "Leo", "Mia", "Noah", "Zoe", "Liam", "Emma", "Oliver", "Ava", "Ethan", "Luna", "James", "Ella", "Logan", "Chloe", "Mason", "Aria", "Lucas", "Lily", "Henry", "Grace", "Jack", "Nora", "Owen", "Hazel", "Ryan", "Maya", "Nathan", "Isla", "Caleb"];
  const lastNames = ["Chen", "Patel", "Kim", "Nguyen", "Garcia", "Martinez", "Lee", "Wang", "Singh", "Ali", "Johnson", "Williams", "Brown", "Jones", "Davis", "Miller", "Wilson", "Moore", "Taylor", "Anderson", "Thomas", "Jackson", "White", "Harris", "Martin", "Thompson", "Young", "Hall", "Allen", "King", "Wright", "Scott", "Green", "Baker", "Hill", "Adams", "Nelson", "Carter", "Mitchell", "Perez"];
  const titles = ["Senior Software Engineer", "Full Stack Developer", "ML Engineer", "Data Scientist", "Backend Engineer", "Frontend Engineer", "DevOps Engineer", "Platform Engineer", "Staff Engineer", "Engineering Manager", "Tech Lead", "Solutions Architect", "Cloud Engineer", "Site Reliability Engineer", "Security Engineer"];
  const locations = ["San Francisco, CA", "New York, NY", "Seattle, WA", "Austin, TX", "Boston, MA", "Chicago, IL", "Denver, CO", "Los Angeles, CA", "Portland, OR", "Miami, FL", "Remote", "Toronto, ON", "London, UK", "Berlin, DE", "Singapore"];
  const eduLevels = ["PhD", "MS", "BS", "MBA"];
  const universities = ["MIT", "Stanford", "Carnegie Mellon", "UC Berkeley", "Georgia Tech", "Caltech", "University of Michigan", "Cornell", "Columbia", "University of Washington", "ETH Zurich", "University of Toronto", "Oxford", "IIT Bombay", "Tsinghua University"];

  return Array.from({ length: count }, (_, i) => {
    const numSkills = 5 + Math.floor(Math.random() * 10);
    const shuffled = [...SKILLS_DB].sort(() => Math.random() - 0.5);
    const skills = shuffled.slice(0, numSkills).map(s => ({
      name: s,
      proficiency: Math.round((2 + Math.random() * 3) * 10) / 10,
    }));

    const technical = 40 + Math.random() * 60;
    const experience = 30 + Math.random() * 70;
    const education = 40 + Math.random() * 60;
    const softSkills = 35 + Math.random() * 65;
    const culturalFit = 40 + Math.random() * 60;
    const yoe = 2 + Math.floor(Math.random() * 15);

    const weights = { technical: 0.35, experience: 0.25, education: 0.15, softSkills: 0.15, culturalFit: 0.10 };
    const raw = [technical, experience, education, softSkills, culturalFit];
    const norm = Math.sqrt(raw.reduce((s, v) => s + v * v, 0));
    const normalized = raw.map(v => v / norm);
    const weighted = normalized.map((v, j) => v * Object.values(weights)[j]);
    const idealDist = Math.sqrt(weighted.reduce((s, v) => s + Math.pow(1 * Object.values(weights)[Object.keys(weights).length - 1] - v, 2), 0));
    const antiIdealDist = Math.sqrt(weighted.reduce((s, v) => s + v * v, 0));
    const topsis = antiIdealDist / (idealDist + antiIdealDist);
    const adjustedTopsis = 0.3 + topsis * 0.65;

    const clusterNames = ["Senior Full-Stack", "ML/AI Specialist", "Cloud/DevOps", "Frontend Expert", "Backend/Systems", "Engineering Leadership"];
    const cluster = clusterNames[Math.floor(Math.random() * clusterNames.length)];

    return {
      id: i + 1,
      name: `${firstNames[i % firstNames.length]} ${lastNames[i % lastNames.length]}`,
      title: titles[Math.floor(Math.random() * titles.length)],
      location: locations[Math.floor(Math.random() * locations.length)],
      email: `candidate${i + 1}@email.com`,
      yoe,
      education: `${eduLevels[Math.floor(Math.random() * eduLevels.length)]}, ${universities[Math.floor(Math.random() * universities.length)]}`,
      previousCompany: COMPANIES[Math.floor(Math.random() * COMPANIES.length)],
      skills,
      scores: { technical, experience, education, softSkills, culturalFit },
      topsis: Math.round(adjustedTopsis * 1000) / 1000,
      cluster,
      status: Math.random() > 0.7 ? "reviewed" : Math.random() > 0.5 ? "shortlisted" : "new",
      appliedDate: new Date(2026, 1, Math.floor(Math.random() * 8) + 1).toISOString().split("T")[0],
      githubStars: Math.floor(Math.random() * 500),
      linkedinConnections: 200 + Math.floor(Math.random() * 800),
    };
  }).sort((a, b) => b.topsis - a.topsis).map((c, i) => ({ ...c, rank: i + 1 }));
}

const competitorData = [
  { dimension: "Technical", yours: 78, competitor: 82 },
  { dimension: "Experience", yours: 72, competitor: 75 },
  { dimension: "Education", yours: 81, competitor: 77 },
  { dimension: "Soft Skills", yours: 69, competitor: 71 },
  { dimension: "Cultural Fit", yours: 74, competitor: 68 },
  { dimension: "Innovation", yours: 70, competitor: 80 },
];

function StatCard({ label, value, sub, color, icon }) {
  return (
    <div style={{
      background: `linear-gradient(135deg, ${COLORS.surface} 0%, ${COLORS.surfaceLight} 100%)`,
      border: `1px solid ${COLORS.border}`,
      borderRadius: 16,
      padding: "20px 24px",
      position: "relative",
      overflow: "hidden",
    }}>
      <div style={{
        position: "absolute", top: -20, right: -20, width: 80, height: 80,
        borderRadius: "50%", background: color, opacity: 0.07,
      }} />
      <div style={{ fontSize: 13, color: COLORS.textDim, marginBottom: 4, letterSpacing: 0.5 }}>{label}</div>
      <div style={{ fontSize: 32, fontWeight: 700, color, lineHeight: 1.1 }}>{value}</div>
      {sub && <div style={{ fontSize: 12, color: COLORS.textMuted, marginTop: 4 }}>{sub}</div>}
    </div>
  );
}

function ScoreBadge({ score }) {
  const color = score >= 0.8 ? COLORS.accent : score >= 0.6 ? COLORS.warning : COLORS.danger;
  return (
    <span style={{
      display: "inline-block",
      padding: "3px 10px",
      borderRadius: 20,
      background: color + "18",
      color,
      fontWeight: 600,
      fontSize: 13,
    }}>
      {score.toFixed(3)}
    </span>
  );
}

function StatusBadge({ status }) {
  const map = {
    new: { bg: COLORS.primary + "20", color: COLORS.primary, label: "New" },
    reviewed: { bg: COLORS.warning + "20", color: COLORS.warning, label: "Reviewed" },
    shortlisted: { bg: COLORS.accent + "20", color: COLORS.accent, label: "Shortlisted" },
  };
  const s = map[status] || map.new;
  return (
    <span style={{
      padding: "2px 10px", borderRadius: 12, background: s.bg,
      color: s.color, fontSize: 11, fontWeight: 600, textTransform: "uppercase", letterSpacing: 0.5,
    }}>{s.label}</span>
  );
}

function MiniBar({ value, max = 100, color = COLORS.primary }) {
  return (
    <div style={{ display: "flex", alignItems: "center", gap: 8 }}>
      <div style={{ flex: 1, height: 6, background: COLORS.surfaceLight, borderRadius: 3, overflow: "hidden" }}>
        <div style={{ width: `${(value / max) * 100}%`, height: "100%", background: color, borderRadius: 3, transition: "width 0.6s ease" }} />
      </div>
      <span style={{ fontSize: 12, color: COLORS.textMuted, minWidth: 28, textAlign: "right" }}>{Math.round(value)}</span>
    </div>
  );
}

function CandidateDetail({ candidate, onClose, competitors }) {
  const radarData = [
    { subject: "Technical", score: candidate.scores.technical, fullMark: 100 },
    { subject: "Experience", score: candidate.scores.experience, fullMark: 100 },
    { subject: "Education", score: candidate.scores.education, fullMark: 100 },
    { subject: "Soft Skills", score: candidate.scores.softSkills, fullMark: 100 },
    { subject: "Cultural Fit", score: candidate.scores.culturalFit, fullMark: 100 },
  ];

  return (
    <div style={{
      position: "fixed", inset: 0, background: "rgba(0,0,0,0.7)", zIndex: 1000,
      display: "flex", justifyContent: "center", alignItems: "flex-start", paddingTop: 40,
      backdropFilter: "blur(4px)",
    }} onClick={onClose}>
      <div style={{
        background: COLORS.bg, border: `1px solid ${COLORS.border}`, borderRadius: 20,
        width: "90%", maxWidth: 900, maxHeight: "85vh", overflow: "auto", padding: 32,
      }} onClick={e => e.stopPropagation()}>
        <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start", marginBottom: 24 }}>
          <div>
            <div style={{ display: "flex", alignItems: "center", gap: 12, marginBottom: 4 }}>
              <span style={{
                width: 44, height: 44, borderRadius: 12, display: "flex", alignItems: "center", justifyContent: "center",
                background: `linear-gradient(135deg, ${COLORS.primary}, ${COLORS.purple})`, color: "#fff", fontWeight: 700, fontSize: 18,
              }}>{candidate.name.split(" ").map(n => n[0]).join("")}</span>
              <div>
                <h2 style={{ margin: 0, color: COLORS.text, fontSize: 22 }}>{candidate.name}</h2>
                <div style={{ color: COLORS.textMuted, fontSize: 14 }}>{candidate.title}</div>
              </div>
            </div>
          </div>
          <button onClick={onClose} style={{
            background: "none", border: "none", color: COLORS.textDim, fontSize: 24, cursor: "pointer", padding: 4,
          }}>✕</button>
        </div>

        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr 1fr 1fr", gap: 12, marginBottom: 24 }}>
          <StatCard label="TOPSIS Score" value={candidate.topsis.toFixed(3)} color={COLORS.accent} />
          <StatCard label="Rank" value={`#${candidate.rank}`} sub={`of ${50} candidates`} color={COLORS.gold} />
          <StatCard label="Experience" value={`${candidate.yoe} yrs`} color={COLORS.primary} />
          <StatCard label="Cluster" value={candidate.cluster.split(" ")[0]} sub={candidate.cluster} color={COLORS.purple} />
        </div>

        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 20, marginBottom: 24 }}>
          <div style={{ background: COLORS.surface, borderRadius: 16, padding: 20, border: `1px solid ${COLORS.border}` }}>
            <h3 style={{ color: COLORS.text, margin: "0 0 16px", fontSize: 15 }}>Score Breakdown</h3>
            <ResponsiveContainer width="100%" height={220}>
              <RadarChart data={radarData}>
                <PolarGrid stroke={COLORS.border} />
                <PolarAngleAxis dataKey="subject" tick={{ fill: COLORS.textMuted, fontSize: 11 }} />
                <PolarRadiusAxis angle={90} domain={[0, 100]} tick={false} axisLine={false} />
                <Radar name="Score" dataKey="score" stroke={COLORS.primary} fill={COLORS.primary} fillOpacity={0.25} strokeWidth={2} />
              </RadarChart>
            </ResponsiveContainer>
          </div>

          <div style={{ background: COLORS.surface, borderRadius: 16, padding: 20, border: `1px solid ${COLORS.border}` }}>
            <h3 style={{ color: COLORS.text, margin: "0 0 16px", fontSize: 15 }}>Dimension Scores</h3>
            {Object.entries(candidate.scores).map(([key, val]) => (
              <div key={key} style={{ marginBottom: 12 }}>
                <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 4 }}>
                  <span style={{ fontSize: 12, color: COLORS.textMuted, textTransform: "capitalize" }}>{key.replace(/([A-Z])/g, " $1")}</span>
                  <span style={{ fontSize: 12, color: COLORS.text, fontWeight: 600 }}>{val.toFixed(1)}/100</span>
                </div>
                <MiniBar value={val} color={val >= 75 ? COLORS.accent : val >= 55 ? COLORS.warning : COLORS.danger} />
              </div>
            ))}
          </div>
        </div>

        <div style={{ background: COLORS.surface, borderRadius: 16, padding: 20, border: `1px solid ${COLORS.border}`, marginBottom: 20 }}>
          <h3 style={{ color: COLORS.text, margin: "0 0 12px", fontSize: 15 }}>Skills</h3>
          <div style={{ display: "flex", flexWrap: "wrap", gap: 6 }}>
            {candidate.skills.map((s, i) => (
              <span key={i} style={{
                padding: "4px 12px", borderRadius: 20, fontSize: 12, fontWeight: 500,
                background: s.proficiency >= 4 ? COLORS.accent + "18" : s.proficiency >= 3 ? COLORS.primary + "18" : COLORS.surfaceLight,
                color: s.proficiency >= 4 ? COLORS.accent : s.proficiency >= 3 ? COLORS.primary : COLORS.textMuted,
                border: `1px solid ${s.proficiency >= 4 ? COLORS.accent + "30" : s.proficiency >= 3 ? COLORS.primary + "30" : COLORS.border}`,
              }}>
                {s.name} <span style={{ opacity: 0.7 }}>({s.proficiency.toFixed(1)})</span>
              </span>
            ))}
          </div>
        </div>

        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr 1fr", gap: 12 }}>
          {[
            { label: "Location", value: candidate.location },
            { label: "Education", value: candidate.education },
            { label: "Previous Company", value: candidate.previousCompany },
            { label: "GitHub Stars", value: candidate.githubStars },
            { label: "LinkedIn Connections", value: candidate.linkedinConnections },
            { label: "Applied", value: candidate.appliedDate },
          ].map((item, i) => (
            <div key={i} style={{ background: COLORS.surfaceLight, borderRadius: 10, padding: "10px 14px" }}>
              <div style={{ fontSize: 11, color: COLORS.textDim, marginBottom: 2 }}>{item.label}</div>
              <div style={{ fontSize: 14, color: COLORS.text }}>{item.value}</div>
            </div>
          ))}
        </div>
      </div>
    </div>
  );
}

function AHPConfig({ weights, setWeights }) {
  const criteria = [
    { key: "technical", label: "Technical Skills", icon: "⚡" },
    { key: "experience", label: "Experience", icon: "📊" },
    { key: "education", label: "Education", icon: "🎓" },
    { key: "softSkills", label: "Soft Skills", icon: "🤝" },
    { key: "culturalFit", label: "Cultural Fit", icon: "🎯" },
  ];

  const total = Object.values(weights).reduce((s, v) => s + v, 0);

  return (
    <div>
      <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 20 }}>
        <div>
          <h2 style={{ margin: 0, color: COLORS.text, fontSize: 20 }}>AHP Weight Configuration</h2>
          <p style={{ margin: "4px 0 0", color: COLORS.textDim, fontSize: 13 }}>Adjust criteria weights using sliders. Total must equal 100%.</p>
        </div>
        <div style={{
          padding: "6px 16px", borderRadius: 20, fontSize: 13, fontWeight: 600,
          background: Math.abs(total - 1) < 0.01 ? COLORS.accent + "20" : COLORS.danger + "20",
          color: Math.abs(total - 1) < 0.01 ? COLORS.accent : COLORS.danger,
        }}>
          Total: {(total * 100).toFixed(0)}%
        </div>
      </div>
      <div style={{ display: "grid", gap: 16 }}>
        {criteria.map(c => (
          <div key={c.key} style={{
            background: COLORS.surface, borderRadius: 12, padding: 16, border: `1px solid ${COLORS.border}`,
          }}>
            <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 8 }}>
              <span style={{ color: COLORS.text, fontSize: 14 }}>{c.icon} {c.label}</span>
              <span style={{ color: COLORS.primary, fontWeight: 700, fontSize: 18 }}>{(weights[c.key] * 100).toFixed(0)}%</span>
            </div>
            <input
              type="range" min={0} max={60} step={1}
              value={weights[c.key] * 100}
              onChange={e => setWeights(prev => ({ ...prev, [c.key]: parseInt(e.target.value) / 100 }))}
              style={{ width: "100%", accentColor: COLORS.primary, cursor: "pointer" }}
            />
          </div>
        ))}
      </div>
    </div>
  );
}

export default function TalentRankApp() {
  const [page, setPage] = useState("dashboard");
  const [candidates] = useState(() => generateCandidates(50));
  const [selectedCandidate, setSelectedCandidate] = useState(null);
  const [searchQuery, setSearchQuery] = useState("");
  const [clusterFilter, setClusterFilter] = useState("all");
  const [statusFilter, setStatusFilter] = useState("all");
  const [sortBy, setSortBy] = useState("rank");
  const [weights, setWeights] = useState({
    technical: 0.35, experience: 0.25, education: 0.15, softSkills: 0.15, culturalFit: 0.10,
  });
  const [uploadModalOpen, setUploadModalOpen] = useState(false);
  const [uploadStep, setUploadStep] = useState(0);

  const clusters = useMemo(() => {
    const map = {};
    candidates.forEach(c => { map[c.cluster] = (map[c.cluster] || 0) + 1; });
    return Object.entries(map).map(([name, count]) => ({ name, count })).sort((a, b) => b.count - a.count);
  }, [candidates]);

  const filteredCandidates = useMemo(() => {
    let list = [...candidates];
    if (searchQuery) list = list.filter(c =>
      c.name.toLowerCase().includes(searchQuery.toLowerCase()) ||
      c.title.toLowerCase().includes(searchQuery.toLowerCase()) ||
      c.skills.some(s => s.name.toLowerCase().includes(searchQuery.toLowerCase()))
    );
    if (clusterFilter !== "all") list = list.filter(c => c.cluster === clusterFilter);
    if (statusFilter !== "all") list = list.filter(c => c.status === statusFilter);
    if (sortBy === "rank") list.sort((a, b) => a.rank - b.rank);
    else if (sortBy === "technical") list.sort((a, b) => b.scores.technical - a.scores.technical);
    else if (sortBy === "experience") list.sort((a, b) => b.scores.experience - a.scores.experience);
    else if (sortBy === "name") list.sort((a, b) => a.name.localeCompare(b.name));
    return list;
  }, [candidates, searchQuery, clusterFilter, statusFilter, sortBy]);

  const navItems = [
    { id: "dashboard", label: "Dashboard", icon: "◈" },
    { id: "rankings", label: "Rankings", icon: "◆" },
    { id: "clusters", label: "Clusters", icon: "◉" },
    { id: "benchmark", label: "Benchmark", icon: "◎" },
    { id: "ahp", label: "AHP Config", icon: "⚙" },
  ];

  const clusterChartData = clusters.map((c, i) => ({
    name: c.name.length > 12 ? c.name.slice(0, 12) + "…" : c.name,
    count: c.count,
    color: [COLORS.primary, COLORS.accent, COLORS.purple, COLORS.pink, COLORS.warning, COLORS.gold][i % 6],
  }));

  const trendData = Array.from({ length: 7 }, (_, i) => ({
    day: ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"][i],
    applications: 15 + Math.floor(Math.random() * 25),
    processed: 10 + Math.floor(Math.random() * 20),
  }));

  function UploadModal() {
    const steps = ["Upload Resume", "Add Links", "Processing", "Complete"];
    return (
      <div style={{
        position: "fixed", inset: 0, background: "rgba(0,0,0,0.7)", zIndex: 1000,
        display: "flex", justifyContent: "center", alignItems: "center", backdropFilter: "blur(4px)",
      }} onClick={() => { setUploadModalOpen(false); setUploadStep(0); }}>
        <div style={{
          background: COLORS.bg, border: `1px solid ${COLORS.border}`, borderRadius: 20,
          width: 520, padding: 32,
        }} onClick={e => e.stopPropagation()}>
          <h2 style={{ color: COLORS.text, margin: "0 0 20px", fontSize: 20 }}>Add New Candidate</h2>

          <div style={{ display: "flex", gap: 4, marginBottom: 28 }}>
            {steps.map((s, i) => (
              <div key={i} style={{ flex: 1, textAlign: "center" }}>
                <div style={{
                  height: 4, borderRadius: 2, marginBottom: 6,
                  background: i <= uploadStep ? COLORS.primary : COLORS.surfaceLight,
                  transition: "background 0.3s",
                }} />
                <span style={{ fontSize: 11, color: i <= uploadStep ? COLORS.primary : COLORS.textDim }}>{s}</span>
              </div>
            ))}
          </div>

          {uploadStep === 0 && (
            <div style={{
              border: `2px dashed ${COLORS.border}`, borderRadius: 16, padding: 40,
              textAlign: "center", cursor: "pointer",
            }} onClick={() => setUploadStep(1)}>
              <div style={{ fontSize: 40, marginBottom: 8 }}>📄</div>
              <div style={{ color: COLORS.text, marginBottom: 4 }}>Drop resume here or click to upload</div>
              <div style={{ color: COLORS.textDim, fontSize: 12 }}>PDF, DOCX, or TXT (max 10MB)</div>
            </div>
          )}

          {uploadStep === 1 && (
            <div style={{ display: "grid", gap: 12 }}>
              {[
                { icon: "🔗", label: "LinkedIn Profile URL", placeholder: "https://linkedin.com/in/..." },
                { icon: "🐙", label: "GitHub Username", placeholder: "github-username" },
                { icon: "📁", label: "Portfolio / Project URL", placeholder: "https://..." },
              ].map((field, i) => (
                <div key={i}>
                  <label style={{ fontSize: 12, color: COLORS.textMuted, marginBottom: 4, display: "block" }}>{field.icon} {field.label}</label>
                  <input
                    style={{
                      width: "100%", padding: "10px 14px", borderRadius: 10, border: `1px solid ${COLORS.border}`,
                      background: COLORS.surface, color: COLORS.text, fontSize: 14, outline: "none", boxSizing: "border-box",
                    }}
                    placeholder={field.placeholder}
                  />
                </div>
              ))}
              <button
                onClick={() => setUploadStep(2)}
                style={{
                  marginTop: 8, padding: "12px 0", borderRadius: 12, border: "none",
                  background: `linear-gradient(135deg, ${COLORS.primary}, ${COLORS.purple})`,
                  color: "#fff", fontSize: 15, fontWeight: 600, cursor: "pointer",
                }}
              >Process Candidate →</button>
            </div>
          )}

          {uploadStep === 2 && (
            <div style={{ textAlign: "center", padding: 20 }}>
              <div style={{ fontSize: 48, marginBottom: 12, animation: "spin 2s linear infinite" }}>⚙️</div>
              <div style={{ color: COLORS.text, marginBottom: 8 }}>Processing candidate data...</div>
              <div style={{ color: COLORS.textDim, fontSize: 13 }}>Running NLP extraction, K-Means classification, and TOPSIS scoring</div>
              <div style={{ margin: "20px auto", width: "80%", height: 6, background: COLORS.surfaceLight, borderRadius: 3, overflow: "hidden" }}>
                <div style={{ width: "70%", height: "100%", background: COLORS.primary, borderRadius: 3, animation: "loading 2s ease-in-out infinite" }} />
              </div>
              <button
                onClick={() => setUploadStep(3)}
                style={{
                  marginTop: 12, padding: "10px 24px", borderRadius: 10, border: `1px solid ${COLORS.border}`,
                  background: COLORS.surface, color: COLORS.textMuted, fontSize: 13, cursor: "pointer",
                }}
              >Skip (Demo)</button>
            </div>
          )}

          {uploadStep === 3 && (
            <div style={{ textAlign: "center", padding: 20 }}>
              <div style={{ fontSize: 48, marginBottom: 12 }}>✅</div>
              <div style={{ color: COLORS.accent, fontSize: 18, fontWeight: 600, marginBottom: 8 }}>Candidate Processed!</div>
              <div style={{ color: COLORS.textMuted, fontSize: 13, marginBottom: 20 }}>TOPSIS Score: 0.847 | Cluster: Senior Full-Stack | Rank: #3</div>
              <button
                onClick={() => { setUploadModalOpen(false); setUploadStep(0); }}
                style={{
                  padding: "12px 32px", borderRadius: 12, border: "none",
                  background: COLORS.accent, color: "#fff", fontSize: 15, fontWeight: 600, cursor: "pointer",
                }}
              >View in Dashboard</button>
            </div>
          )}
        </div>
      </div>
    );
  }

  return (
    <div style={{
      display: "flex", minHeight: "100vh", background: COLORS.bg, fontFamily: "'DM Sans', 'Segoe UI', sans-serif", color: COLORS.text,
    }}>
      <style>{`
        @import url('https://fonts.googleapis.com/css2?family=DM+Sans:ital,opsz,wght@0,9..40,300;0,9..40,400;0,9..40,500;0,9..40,600;0,9..40,700&display=swap');
        * { box-sizing: border-box; }
        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: ${COLORS.border}; border-radius: 3px; }
        @keyframes loading { 0%{width:20%} 50%{width:80%} 100%{width:20%} }
      `}</style>

      {/* Sidebar */}
      <div style={{
        width: 220, background: COLORS.surface, borderRight: `1px solid ${COLORS.border}`,
        display: "flex", flexDirection: "column", flexShrink: 0,
      }}>
        <div style={{ padding: "20px 20px 24px", borderBottom: `1px solid ${COLORS.border}` }}>
          <div style={{ fontSize: 20, fontWeight: 700, color: COLORS.text, letterSpacing: -0.5 }}>
            <span style={{ color: COLORS.primary }}>Talent</span>Rank
          </div>
          <div style={{ fontSize: 11, color: COLORS.textDim, marginTop: 2 }}>AI Recruitment Platform</div>
        </div>

        <nav style={{ flex: 1, padding: "12px 10px" }}>
          {navItems.map(item => (
            <button
              key={item.id}
              onClick={() => setPage(item.id)}
              style={{
                width: "100%", display: "flex", alignItems: "center", gap: 10,
                padding: "10px 14px", borderRadius: 10, border: "none", cursor: "pointer",
                background: page === item.id ? COLORS.primary + "18" : "transparent",
                color: page === item.id ? COLORS.primary : COLORS.textMuted,
                fontSize: 14, fontWeight: page === item.id ? 600 : 400,
                marginBottom: 2, textAlign: "left", transition: "all 0.2s",
              }}
            >
              <span style={{ fontSize: 16 }}>{item.icon}</span>
              {item.label}
            </button>
          ))}
        </nav>

        <div style={{ padding: 16, borderTop: `1px solid ${COLORS.border}` }}>
          <button
            onClick={() => setUploadModalOpen(true)}
            style={{
              width: "100%", padding: "10px 0", borderRadius: 10, border: "none",
              background: `linear-gradient(135deg, ${COLORS.primary}, ${COLORS.purple})`,
              color: "#fff", fontSize: 13, fontWeight: 600, cursor: "pointer",
            }}
          >+ Add Candidate</button>
        </div>
      </div>

      {/* Main Content */}
      <div style={{ flex: 1, overflow: "auto", padding: 28 }}>

        {/* DASHBOARD */}
        {page === "dashboard" && (
          <div>
            <h1 style={{ margin: "0 0 4px", fontSize: 24, fontWeight: 700 }}>Dashboard</h1>
            <p style={{ margin: "0 0 24px", color: COLORS.textDim, fontSize: 14 }}>Senior Software Engineer — Pipeline Overview</p>

            <div style={{ display: "grid", gridTemplateColumns: "repeat(4, 1fr)", gap: 16, marginBottom: 24 }}>
              <StatCard label="TOTAL CANDIDATES" value={candidates.length} sub="↑ 12 this week" color={COLORS.primary} />
              <StatCard label="AVG TOPSIS SCORE" value={(candidates.reduce((s, c) => s + c.topsis, 0) / candidates.length).toFixed(3)} sub="Above threshold" color={COLORS.accent} />
              <StatCard label="CLUSTERS" value={clusters.length} sub="Auto-classified" color={COLORS.purple} />
              <StatCard label="SHORTLISTED" value={candidates.filter(c => c.status === "shortlisted").length} sub="Ready for interview" color={COLORS.gold} />
            </div>

            <div style={{ display: "grid", gridTemplateColumns: "2fr 1fr", gap: 16, marginBottom: 24 }}>
              <div style={{ background: COLORS.surface, borderRadius: 16, padding: 20, border: `1px solid ${COLORS.border}` }}>
                <h3 style={{ margin: "0 0 16px", fontSize: 15, color: COLORS.text }}>Application Trend (This Week)</h3>
                <ResponsiveContainer width="100%" height={200}>
                  <LineChart data={trendData}>
                    <XAxis dataKey="day" axisLine={false} tickLine={false} tick={{ fill: COLORS.textDim, fontSize: 12 }} />
                    <YAxis axisLine={false} tickLine={false} tick={{ fill: COLORS.textDim, fontSize: 12 }} />
                    <Tooltip contentStyle={{ background: COLORS.surface, border: `1px solid ${COLORS.border}`, borderRadius: 8, fontSize: 12 }} />
                    <Line type="monotone" dataKey="applications" stroke={COLORS.primary} strokeWidth={2} dot={false} />
                    <Line type="monotone" dataKey="processed" stroke={COLORS.accent} strokeWidth={2} dot={false} strokeDasharray="5 5" />
                  </LineChart>
                </ResponsiveContainer>
              </div>

              <div style={{ background: COLORS.surface, borderRadius: 16, padding: 20, border: `1px solid ${COLORS.border}` }}>
                <h3 style={{ margin: "0 0 16px", fontSize: 15, color: COLORS.text }}>Cluster Distribution</h3>
                <ResponsiveContainer width="100%" height={200}>
                  <BarChart data={clusterChartData} layout="vertical">
                    <XAxis type="number" axisLine={false} tickLine={false} tick={{ fill: COLORS.textDim, fontSize: 11 }} />
                    <YAxis type="category" dataKey="name" axisLine={false} tickLine={false} tick={{ fill: COLORS.textMuted, fontSize: 11 }} width={90} />
                    <Bar dataKey="count" radius={[0, 4, 4, 0]} barSize={16}>
                      {clusterChartData.map((d, i) => <Cell key={i} fill={d.color} />)}
                    </Bar>
                  </BarChart>
                </ResponsiveContainer>
              </div>
            </div>

            <div style={{ background: COLORS.surface, borderRadius: 16, padding: 20, border: `1px solid ${COLORS.border}` }}>
              <h3 style={{ margin: "0 0 16px", fontSize: 15, color: COLORS.text }}>Top 10 Candidates</h3>
              <table style={{ width: "100%", borderCollapse: "collapse" }}>
                <thead>
                  <tr>
                    {["Rank", "Candidate", "TOPSIS", "Technical", "Experience", "Cluster", "Status"].map(h => (
                      <th key={h} style={{ padding: "8px 12px", textAlign: "left", fontSize: 11, color: COLORS.textDim, borderBottom: `1px solid ${COLORS.border}`, fontWeight: 500, textTransform: "uppercase", letterSpacing: 0.5 }}>{h}</th>
                    ))}
                  </tr>
                </thead>
                <tbody>
                  {candidates.slice(0, 10).map(c => (
                    <tr key={c.id} onClick={() => setSelectedCandidate(c)} style={{ cursor: "pointer", transition: "background 0.15s" }}
                      onMouseEnter={e => e.currentTarget.style.background = COLORS.surfaceLight}
                      onMouseLeave={e => e.currentTarget.style.background = "transparent"}>
                      <td style={{ padding: "10px 12px", fontSize: 14, fontWeight: 700, color: c.rank <= 3 ? COLORS.gold : COLORS.textMuted }}>#{c.rank}</td>
                      <td style={{ padding: "10px 12px" }}>
                        <div style={{ fontSize: 14, color: COLORS.text, fontWeight: 500 }}>{c.name}</div>
                        <div style={{ fontSize: 12, color: COLORS.textDim }}>{c.title}</div>
                      </td>
                      <td style={{ padding: "10px 12px" }}><ScoreBadge score={c.topsis} /></td>
                      <td style={{ padding: "10px 12px", width: 120 }}><MiniBar value={c.scores.technical} color={COLORS.primary} /></td>
                      <td style={{ padding: "10px 12px", width: 120 }}><MiniBar value={c.scores.experience} color={COLORS.purple} /></td>
                      <td style={{ padding: "10px 12px", fontSize: 12, color: COLORS.textMuted }}>{c.cluster}</td>
                      <td style={{ padding: "10px 12px" }}><StatusBadge status={c.status} /></td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>
          </div>
        )}

        {/* RANKINGS */}
        {page === "rankings" && (
          <div>
            <h1 style={{ margin: "0 0 4px", fontSize: 24, fontWeight: 700 }}>Candidate Rankings</h1>
            <p style={{ margin: "0 0 20px", color: COLORS.textDim, fontSize: 14 }}>Full TOPSIS-ranked candidate list with filters</p>

            <div style={{ display: "flex", gap: 10, marginBottom: 20, flexWrap: "wrap" }}>
              <input
                value={searchQuery} onChange={e => setSearchQuery(e.target.value)}
                placeholder="Search by name, title, or skill..."
                style={{
                  flex: 1, minWidth: 200, padding: "9px 14px", borderRadius: 10, border: `1px solid ${COLORS.border}`,
                  background: COLORS.surface, color: COLORS.text, fontSize: 13, outline: "none",
                }}
              />
              <select value={clusterFilter} onChange={e => setClusterFilter(e.target.value)}
                style={{ padding: "9px 14px", borderRadius: 10, border: `1px solid ${COLORS.border}`, background: COLORS.surface, color: COLORS.text, fontSize: 13 }}>
                <option value="all">All Clusters</option>
                {clusters.map(c => <option key={c.name} value={c.name}>{c.name}</option>)}
              </select>
              <select value={statusFilter} onChange={e => setStatusFilter(e.target.value)}
                style={{ padding: "9px 14px", borderRadius: 10, border: `1px solid ${COLORS.border}`, background: COLORS.surface, color: COLORS.text, fontSize: 13 }}>
                <option value="all">All Status</option>
                <option value="new">New</option>
                <option value="reviewed">Reviewed</option>
                <option value="shortlisted">Shortlisted</option>
              </select>
              <select value={sortBy} onChange={e => setSortBy(e.target.value)}
                style={{ padding: "9px 14px", borderRadius: 10, border: `1px solid ${COLORS.border}`, background: COLORS.surface, color: COLORS.text, fontSize: 13 }}>
                <option value="rank">Sort: TOPSIS Rank</option>
                <option value="technical">Sort: Technical</option>
                <option value="experience">Sort: Experience</option>
                <option value="name">Sort: Name</option>
              </select>
            </div>

            <div style={{ fontSize: 12, color: COLORS.textDim, marginBottom: 12 }}>
              Showing {filteredCandidates.length} of {candidates.length} candidates
            </div>

            <div style={{ background: COLORS.surface, borderRadius: 16, border: `1px solid ${COLORS.border}`, overflow: "hidden" }}>
              <table style={{ width: "100%", borderCollapse: "collapse" }}>
                <thead>
                  <tr>
                    {["#", "Candidate", "TOPSIS", "Tech", "Exp", "Edu", "Soft", "Culture", "YoE", "Cluster", "Status"].map(h => (
                      <th key={h} style={{ padding: "10px 10px", textAlign: "left", fontSize: 11, color: COLORS.textDim, borderBottom: `1px solid ${COLORS.border}`, fontWeight: 500, textTransform: "uppercase", letterSpacing: 0.3 }}>{h}</th>
                    ))}
                  </tr>
                </thead>
                <tbody>
                  {filteredCandidates.slice(0, 30).map(c => (
                    <tr key={c.id} onClick={() => setSelectedCandidate(c)} style={{ cursor: "pointer", borderBottom: `1px solid ${COLORS.border}08` }}
                      onMouseEnter={e => e.currentTarget.style.background = COLORS.surfaceLight}
                      onMouseLeave={e => e.currentTarget.style.background = "transparent"}>
                      <td style={{ padding: "8px 10px", fontSize: 13, fontWeight: 700, color: c.rank <= 3 ? COLORS.gold : COLORS.textDim }}>#{c.rank}</td>
                      <td style={{ padding: "8px 10px" }}>
                        <div style={{ fontSize: 13, color: COLORS.text, fontWeight: 500 }}>{c.name}</div>
                        <div style={{ fontSize: 11, color: COLORS.textDim }}>{c.title}</div>
                      </td>
                      <td style={{ padding: "8px 10px" }}><ScoreBadge score={c.topsis} /></td>
                      {["technical", "experience", "education", "softSkills", "culturalFit"].map(k => (
                        <td key={k} style={{ padding: "8px 6px", fontSize: 12, color: c.scores[k] >= 75 ? COLORS.accent : c.scores[k] >= 55 ? COLORS.text : COLORS.danger }}>
                          {c.scores[k].toFixed(0)}
                        </td>
                      ))}
                      <td style={{ padding: "8px 10px", fontSize: 12, color: COLORS.textMuted }}>{c.yoe}y</td>
                      <td style={{ padding: "8px 10px", fontSize: 11, color: COLORS.textMuted }}>{c.cluster.split(" ").slice(0, 2).join(" ")}</td>
                      <td style={{ padding: "8px 10px" }}><StatusBadge status={c.status} /></td>
                    </tr>
                  ))}
                </tbody>
              </table>
            </div>
          </div>
        )}

        {/* CLUSTERS */}
        {page === "clusters" && (
          <div>
            <h1 style={{ margin: "0 0 4px", fontSize: 24, fontWeight: 700 }}>Cluster Explorer</h1>
            <p style={{ margin: "0 0 24px", color: COLORS.textDim, fontSize: 14 }}>K-Means++ auto-classified candidate groups</p>

            <div style={{ display: "grid", gridTemplateColumns: "repeat(3, 1fr)", gap: 16 }}>
              {clusters.map((cluster, i) => {
                const clusterCandidates = candidates.filter(c => c.cluster === cluster.name);
                const avgTopsis = clusterCandidates.reduce((s, c) => s + c.topsis, 0) / clusterCandidates.length;
                const avgTech = clusterCandidates.reduce((s, c) => s + c.scores.technical, 0) / clusterCandidates.length;
                const avgExp = clusterCandidates.reduce((s, c) => s + c.scores.experience, 0) / clusterCandidates.length;
                const colors = [COLORS.primary, COLORS.accent, COLORS.purple, COLORS.pink, COLORS.warning, COLORS.gold];
                const clr = colors[i % colors.length];

                return (
                  <div key={cluster.name} style={{
                    background: COLORS.surface, borderRadius: 16, border: `1px solid ${COLORS.border}`,
                    padding: 20, position: "relative", overflow: "hidden",
                  }}>
                    <div style={{
                      position: "absolute", top: -30, right: -30, width: 100, height: 100,
                      borderRadius: "50%", background: clr, opacity: 0.06,
                    }} />
                    <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start", marginBottom: 16 }}>
                      <div>
                        <div style={{ fontSize: 16, fontWeight: 600, color: COLORS.text, marginBottom: 2 }}>{cluster.name}</div>
                        <div style={{ fontSize: 12, color: COLORS.textDim }}>{cluster.count} candidates</div>
                      </div>
                      <div style={{
                        width: 36, height: 36, borderRadius: 10, display: "flex", alignItems: "center", justifyContent: "center",
                        background: clr + "20", color: clr, fontWeight: 700, fontSize: 14,
                      }}>{cluster.count}</div>
                    </div>

                    <div style={{ marginBottom: 12 }}>
                      <div style={{ display: "flex", justifyContent: "space-between", fontSize: 12, marginBottom: 4 }}>
                        <span style={{ color: COLORS.textDim }}>Avg TOPSIS</span>
                        <span style={{ color: clr, fontWeight: 600 }}>{avgTopsis.toFixed(3)}</span>
                      </div>
                      <MiniBar value={avgTopsis * 100} color={clr} />
                    </div>
                    <div style={{ marginBottom: 12 }}>
                      <div style={{ display: "flex", justifyContent: "space-between", fontSize: 12, marginBottom: 4 }}>
                        <span style={{ color: COLORS.textDim }}>Avg Technical</span>
                        <span style={{ color: COLORS.textMuted }}>{avgTech.toFixed(0)}</span>
                      </div>
                      <MiniBar value={avgTech} color={COLORS.primary} />
                    </div>
                    <div>
                      <div style={{ display: "flex", justifyContent: "space-between", fontSize: 12, marginBottom: 4 }}>
                        <span style={{ color: COLORS.textDim }}>Avg Experience</span>
                        <span style={{ color: COLORS.textMuted }}>{avgExp.toFixed(0)}</span>
                      </div>
                      <MiniBar value={avgExp} color={COLORS.purple} />
                    </div>

                    <div style={{ marginTop: 16, borderTop: `1px solid ${COLORS.border}`, paddingTop: 12 }}>
                      <div style={{ fontSize: 11, color: COLORS.textDim, marginBottom: 6 }}>Top Candidates</div>
                      {clusterCandidates.slice(0, 3).map(c => (
                        <div key={c.id} onClick={() => setSelectedCandidate(c)} style={{
                          display: "flex", justifyContent: "space-between", alignItems: "center",
                          padding: "4px 0", cursor: "pointer", fontSize: 12,
                        }}>
                          <span style={{ color: COLORS.text }}>{c.name}</span>
                          <ScoreBadge score={c.topsis} />
                        </div>
                      ))}
                    </div>
                  </div>
                );
              })}
            </div>
          </div>
        )}

        {/* BENCHMARK */}
        {page === "benchmark" && (
          <div>
            <h1 style={{ margin: "0 0 4px", fontSize: 24, fontWeight: 700 }}>Competitor Benchmark</h1>
            <p style={{ margin: "0 0 24px", color: COLORS.textDim, fontSize: 14 }}>Your talent pipeline vs. competitor talent (Google Engineering)</p>

            <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 16, marginBottom: 24 }}>
              <div style={{ background: COLORS.surface, borderRadius: 16, padding: 20, border: `1px solid ${COLORS.border}` }}>
                <h3 style={{ margin: "0 0 16px", fontSize: 15, color: COLORS.text }}>Talent Radar Comparison</h3>
                <ResponsiveContainer width="100%" height={300}>
                  <RadarChart data={competitorData}>
                    <PolarGrid stroke={COLORS.border} />
                    <PolarAngleAxis dataKey="dimension" tick={{ fill: COLORS.textMuted, fontSize: 12 }} />
                    <PolarRadiusAxis angle={90} domain={[0, 100]} tick={false} axisLine={false} />
                    <Radar name="Your Pipeline" dataKey="yours" stroke={COLORS.primary} fill={COLORS.primary} fillOpacity={0.2} strokeWidth={2} />
                    <Radar name="Competitor" dataKey="competitor" stroke={COLORS.danger} fill={COLORS.danger} fillOpacity={0.1} strokeWidth={2} strokeDasharray="5 5" />
                    <Tooltip contentStyle={{ background: COLORS.surface, border: `1px solid ${COLORS.border}`, borderRadius: 8, fontSize: 12 }} />
                  </RadarChart>
                </ResponsiveContainer>
                <div style={{ display: "flex", justifyContent: "center", gap: 24, marginTop: 8 }}>
                  <div style={{ display: "flex", alignItems: "center", gap: 6, fontSize: 12 }}>
                    <div style={{ width: 12, height: 3, background: COLORS.primary, borderRadius: 2 }} />
                    <span style={{ color: COLORS.textMuted }}>Your Pipeline</span>
                  </div>
                  <div style={{ display: "flex", alignItems: "center", gap: 6, fontSize: 12 }}>
                    <div style={{ width: 12, height: 3, background: COLORS.danger, borderRadius: 2, borderTop: "1px dashed" + COLORS.danger }} />
                    <span style={{ color: COLORS.textMuted }}>Google (Competitor)</span>
                  </div>
                </div>
              </div>

              <div style={{ background: COLORS.surface, borderRadius: 16, padding: 20, border: `1px solid ${COLORS.border}` }}>
                <h3 style={{ margin: "0 0 16px", fontSize: 15, color: COLORS.text }}>Gap Analysis</h3>
                {competitorData.map(d => {
                  const gap = d.yours - d.competitor;
                  return (
                    <div key={d.dimension} style={{ marginBottom: 16 }}>
                      <div style={{ display: "flex", justifyContent: "space-between", marginBottom: 6 }}>
                        <span style={{ fontSize: 13, color: COLORS.text }}>{d.dimension}</span>
                        <span style={{
                          fontSize: 13, fontWeight: 600,
                          color: gap >= 0 ? COLORS.accent : COLORS.danger,
                        }}>
                          {gap >= 0 ? "+" : ""}{gap} pts
                        </span>
                      </div>
                      <div style={{ display: "flex", gap: 4, alignItems: "center" }}>
                        <div style={{ flex: 1, height: 8, background: COLORS.surfaceLight, borderRadius: 4, overflow: "hidden", position: "relative" }}>
                          <div style={{
                            position: "absolute", left: 0, top: 0, height: "100%",
                            width: `${d.yours}%`, background: COLORS.primary, borderRadius: 4, opacity: 0.8,
                          }} />
                          <div style={{
                            position: "absolute", left: 0, top: 0, height: "100%",
                            width: `${d.competitor}%`, borderRight: `2px solid ${COLORS.danger}`,
                          }} />
                        </div>
                      </div>
                      <div style={{ display: "flex", justifyContent: "space-between", marginTop: 2 }}>
                        <span style={{ fontSize: 10, color: COLORS.primary }}>You: {d.yours}</span>
                        <span style={{ fontSize: 10, color: COLORS.danger }}>Comp: {d.competitor}</span>
                      </div>
                    </div>
                  );
                })}
              </div>
            </div>

            <div style={{ display: "grid", gridTemplateColumns: "repeat(3, 1fr)", gap: 16 }}>
              <StatCard label="MARKET POSITION INDEX" value="74/100" sub="Above average" color={COLORS.accent} />
              <StatCard label="TALENT GAP" value="-2.3%" sub="Narrowing (was -5.1%)" color={COLORS.warning} />
              <StatCard label="CANDIDATES ABOVE BENCHMARK" value={`${candidates.filter(c => c.topsis > 0.75).length}`} sub={`of ${candidates.length} total`} color={COLORS.primary} />
            </div>
          </div>
        )}

        {/* AHP CONFIG */}
        {page === "ahp" && (
          <div style={{ maxWidth: 640 }}>
            <AHPConfig weights={weights} setWeights={setWeights} />
          </div>
        )}
      </div>

      {selectedCandidate && <CandidateDetail candidate={selectedCandidate} onClose={() => setSelectedCandidate(null)} />}
      {uploadModalOpen && <UploadModal />}
    </div>
  );
}

