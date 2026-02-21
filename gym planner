import { useState } from "react";

const GOALS = [
  { icon: "🔥", title: "Burn Fat", desc: "Lose weight & get lean" },
  { icon: "💪", title: "Build Muscle", desc: "Get bigger & stronger" },
  { icon: "⚡", title: "Get Fit", desc: "Improve stamina & health" },
  { icon: "🏋️", title: "Get Strong", desc: "Lift heavier, get powerful" },
];

const DIET_TAGS = ["No Preference", "High Protein", "Vegetarian", "Vegan", "Keto", "No Dairy", "Gluten Free", "Halal"];

export default function App() {
  const [screen, setScreen] = useState("landing");
  const [formStep, setFormStep] = useState(1);
  const [planTab, setPlanTab] = useState("workout");
  const [openDay, setOpenDay] = useState(0);
  const [plan, setPlan] = useState(null);
  const [errorMsg, setErrorMsg] = useState("");

  const [form, setForm] = useState({
    goal: "", age: "", sex: "", weight: "", height: "",
    experience: "", equipment: "", days: "", diet: [], notes: "",
  });

  const set = (k, v) => setForm(f => ({ ...f, [k]: v }));
  const toggleDiet = tag => set("diet", form.diet.includes(tag)
    ? form.diet.filter(d => d !== tag)
    : [...form.diet, tag]);

  const canNext = () => {
    if (formStep === 1) return form.goal;
    if (formStep === 2) return form.age && form.sex && form.weight && form.height && form.experience;
    if (formStep === 3) return form.equipment && form.days;
    return true;
  };

  const buildPrompt = () => `
You are an expert personal trainer and sports nutritionist. Create a complete, beginner-friendly, highly personalised training and nutrition plan.

USER PROFILE:
- Goal: ${form.goal}
- Age: ${form.age} | Sex: ${form.sex}
- Weight: ${form.weight} kg | Height: ${form.height} cm
- Experience level: ${form.experience}
- Equipment available: ${form.equipment}
- Training days per week: ${form.days}
- Diet preferences: ${form.diet.join(", ") || "None"}
- Extra notes: ${form.notes || "None"}

Respond ONLY with a valid JSON object — no markdown, no explanation, no code fences. Use this exact shape:
{
  "name": "Short punchy plan name",
  "summary": "2-3 sentences personalised to this user explaining their plan",
  "calories": 2400,
  "protein": 180,
  "carbs": 240,
  "fat": 70,
  "workoutDays": [
    {
      "day": "Monday – Push",
      "exercises": [
        { "name": "Barbell Bench Press", "sets": "3 × 8–10 reps", "rest": "90s rest", "note": "Keep your back flat on the bench" }
      ]
    }
  ],
  "restDays": ["Thursday", "Saturday", "Sunday"],
  "meals": [
    { "meal": "Breakfast", "foods": ["4 scrambled eggs", "2 slices wholegrain toast", "1 banana"], "calories": 550 },
    { "meal": "Lunch", "foods": ["Grilled chicken breast 200g", "Brown rice 150g", "Steamed broccoli"], "calories": 680 },
    { "meal": "Dinner", "foods": ["Salmon fillet", "Sweet potato", "Green salad with olive oil"], "calories": 720 },
    { "meal": "Snack", "foods": ["Greek yoghurt", "Handful of almonds"], "calories": 280 }
  ],
  "tips": [
    "Drink at least 2-3 litres of water daily.",
    "Sleep 7-9 hours - that is when muscle actually grows.",
    "Add a little weight or one extra rep each week to keep progressing."
  ]
}`;

  const generate = async () => {
    setScreen("loading");
    setErrorMsg("");
    try {
      const res = await fetch("https://api.anthropic.com/v1/messages", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          model: "claude-sonnet-4-20250514",
          max_tokens: 1000,
          messages: [{ role: "user", content: buildPrompt() }],
        }),
      });
      const data = await res.json();
      const raw = data.content?.map(c => c.text || "").join("") || "";
      const clean = raw.replace(/```json|```/gi, "").trim();
      const parsed = JSON.parse(clean);
      setPlan(parsed);
      setScreen("plan");
      setOpenDay(0);
      setPlanTab("workout");
    } catch (e) {
      setErrorMsg("Something went wrong generating your plan. Please try again.");
      setScreen("form");
      setFormStep(4);
    }
  };

  const S = {
    app: { minHeight: "100vh", background: "#0b0b0b", color: "#fff", fontFamily: "'Sora', sans-serif", overflowX: "hidden" },
    wrap: { maxWidth: 520, margin: "0 auto", padding: "40px 22px 100px", position: "relative", zIndex: 1 },
    label: { display: "block", marginBottom: 8, fontSize: 11, fontWeight: 700, color: "#666", textTransform: "uppercase", letterSpacing: "0.1em" },
    input: { width: "100%", padding: "14px 16px", borderRadius: 12, border: "1.5px solid rgba(255,255,255,0.1)", background: "rgba(255,255,255,0.05)", color: "#fff", fontSize: 15, fontFamily: "'Sora', sans-serif", outline: "none", boxSizing: "border-box" },
    btn: { width: "100%", padding: "16px", borderRadius: 14, border: "none", background: "#B5FF47", color: "#0b0b0b", fontSize: 16, fontWeight: 800, cursor: "pointer", fontFamily: "'Sora', sans-serif", letterSpacing: "0.02em" },
    ghost: { background: "rgba(255,255,255,0.06)", border: "1px solid rgba(255,255,255,0.1)", borderRadius: 8, padding: "8px 14px", color: "#aaa", cursor: "pointer", fontSize: 12, fontFamily: "'Sora', sans-serif", fontWeight: 600 },
  };

  const Blob = ({ style }) => (
    <div style={{ position: "fixed", borderRadius: "50%", filter: "blur(130px)", opacity: 0.13, pointerEvents: "none", zIndex: 0, ...style }} />
  );

  const ProgressBar = ({ total, current }) => (
    <div style={{ display: "flex", gap: 6, marginBottom: 32 }}>
      {Array.from({ length: total }).map((_, i) => (
        <div key={i} style={{ flex: 1, height: 3, borderRadius: 99, background: i < current ? "#B5FF47" : "rgba(255,255,255,0.1)", transition: "background 0.3s" }} />
      ))}
    </div>
  );

  const SectionTitle = ({ step, title, sub }) => (
    <div style={{ marginBottom: 28 }}>
      <div style={{ fontSize: 11, color: "#B5FF47", fontWeight: 700, letterSpacing: "0.1em", marginBottom: 8 }}>STEP {step} OF 4</div>
      <h2 style={{ fontFamily: "'Bebas Neue', sans-serif", fontSize: 44, margin: 0, letterSpacing: 1, lineHeight: 1 }}>{title}</h2>
      {sub && <p style={{ color: "#777", fontSize: 14, marginTop: 10, lineHeight: 1.5 }}>{sub}</p>}
    </div>
  );

  const ChoiceBtn = ({ selected, onClick, icon, title, sub }) => (
    <button onClick={onClick} style={{
      display: "flex", justifyContent: "space-between", alignItems: "center",
      padding: "15px 16px", borderRadius: 12, cursor: "pointer", textAlign: "left", width: "100%",
      border: selected ? "2px solid #B5FF47" : "2px solid rgba(255,255,255,0.08)",
      background: selected ? "rgba(181,255,71,0.08)" : "rgba(255,255,255,0.03)",
      marginBottom: 8,
    }}>
      <div style={{ display: "flex", alignItems: "center", gap: 12 }}>
        {icon && <span style={{ fontSize: 20 }}>{icon}</span>}
        <div>
          <div style={{ fontWeight: 700, fontSize: 14, color: selected ? "#B5FF47" : "#fff", fontFamily: "'Sora', sans-serif" }}>{title}</div>
          {sub && <div style={{ fontSize: 12, color: "#666", marginTop: 2, fontFamily: "'Sora', sans-serif" }}>{sub}</div>}
        </div>
      </div>
      {selected && <span style={{ color: "#B5FF47", fontSize: 16 }}>✓</span>}
    </button>
  );

  // ── LANDING ──────────────────────────────────────────────────────────────
  if (screen === "landing") return (
    <div style={S.app}>
      <link href="https://fonts.googleapis.com/css2?family=Sora:wght@400;600;700;800&family=Bebas+Neue&display=swap" rel="stylesheet" />
      <Blob style={{ width: 500, height: 500, background: "#B5FF47", top: -150, left: -150 }} />
      <Blob style={{ width: 400, height: 400, background: "#00C2FF", bottom: -100, right: -100 }} />
      <div style={{ ...S.wrap, paddingTop: 90, textAlign: "center" }}>
        <div style={{ display: "inline-block", background: "rgba(181,255,71,0.1)", border: "1px solid rgba(181,255,71,0.25)", borderRadius: 50, padding: "6px 18px", fontSize: 11, color: "#B5FF47", fontWeight: 700, letterSpacing: "0.08em", marginBottom: 36 }}>
          ✦ AI-POWERED · FREE · BUILT FOR BEGINNERS
        </div>
        <h1 style={{ fontFamily: "'Bebas Neue', sans-serif", fontSize: "clamp(76px, 18vw, 130px)", lineHeight: 0.85, margin: "0 0 28px", letterSpacing: 2 }}>
          YOUR<br /><span style={{ color: "#B5FF47" }}>PERFECT</span><br />GYM PLAN
        </h1>
        <p style={{ fontSize: 17, color: "#888", lineHeight: 1.7, maxWidth: 360, margin: "0 auto 48px" }}>
          No personal trainer needed. Answer 4 quick questions and get a fully custom workout + nutrition plan — in seconds.
        </p>
        <div style={{ display: "flex", flexWrap: "wrap", gap: 8, justifyContent: "center", marginBottom: 48 }}>
          {["📋 Weekly Workout Plan", "🥗 Full Meal Plan", "📊 Macro Targets", "💡 Beginner Tips"].map(f => (
            <span key={f} style={{ background: "rgba(255,255,255,0.06)", border: "1px solid rgba(255,255,255,0.1)", borderRadius: 50, padding: "8px 16px", fontSize: 13, color: "#ccc" }}>{f}</span>
          ))}
        </div>
        <button onClick={() => { setScreen("form"); setFormStep(1); }} style={{ ...S.btn, fontSize: 18, padding: "18px 52px", width: "auto", borderRadius: 50 }}>
          Build My Free Plan →
        </button>
        <p style={{ color: "#444", fontSize: 13, marginTop: 16 }}>No signup required · Takes 60 seconds</p>
      </div>
    </div>
  );

  // ── LOADING ──────────────────────────────────────────────────────────────
  if (screen === "loading") return (
    <div style={{ ...S.app, display: "flex", alignItems: "center", justifyContent: "center" }}>
      <link href="https://fonts.googleapis.com/css2?family=Sora:wght@400;600;700;800&family=Bebas+Neue&display=swap" rel="stylesheet" />
      <Blob style={{ width: 600, height: 600, background: "#B5FF47", top: "50%", left: "50%", transform: "translate(-50%,-50%)" }} />
      <div style={{ textAlign: "center", position: "relative", zIndex: 1 }}>
        <div style={{ width: 72, height: 72, borderRadius: "50%", border: "3px solid rgba(181,255,71,0.15)", borderTop: "3px solid #B5FF47", animation: "spin 1s linear infinite", margin: "0 auto 28px" }} />
        <style>{`@keyframes spin { to { transform: rotate(360deg); } }`}</style>
        <h2 style={{ fontFamily: "'Bebas Neue', sans-serif", fontSize: 40, margin: "0 0 10px", letterSpacing: 1 }}>BUILDING YOUR PLAN</h2>
        <p style={{ color: "#777", fontSize: 14 }}>AI is crafting your personalised workout & nutrition...</p>
      </div>
    </div>
  );

  // ── FORM ─────────────────────────────────────────────────────────────────
  if (screen === "form") return (
    <div style={S.app}>
      <link href="https://fonts.googleapis.com/css2?family=Sora:wght@400;600;700;800&family=Bebas+Neue&display=swap" rel="stylesheet" />
      <Blob style={{ width: 400, height: 400, background: "#B5FF47", top: -100, right: -100 }} />
      <div style={S.wrap}>
        <button onClick={() => formStep === 1 ? setScreen("landing") : setFormStep(s => s - 1)}
          style={{ background: "none", border: "none", color: "#555", cursor: "pointer", fontSize: 14, padding: 0, marginBottom: 24, fontFamily: "'Sora', sans-serif" }}>
          ← Back
        </button>
        <ProgressBar total={4} current={formStep} />

        {/* STEP 1 */}
        {formStep === 1 && (
          <>
            <SectionTitle step={1} title="WHAT'S YOUR GOAL?" sub="Pick the one that matters most right now." />
            <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 10, marginBottom: 32 }}>
              {GOALS.map(g => (
                <button key={g.title} onClick={() => set("goal", g.title)} style={{
                  background: form.goal === g.title ? "rgba(181,255,71,0.1)" : "rgba(255,255,255,0.04)",
                  border: form.goal === g.title ? "2px solid #B5FF47" : "2px solid rgba(255,255,255,0.08)",
                  borderRadius: 16, padding: "20px 16px", cursor: "pointer", textAlign: "left", transition: "all 0.2s",
                }}>
                  <div style={{ fontSize: 28, marginBottom: 8 }}>{g.icon}</div>
                  <div style={{ fontWeight: 700, fontSize: 14, color: form.goal === g.title ? "#B5FF47" : "#fff", fontFamily: "'Sora', sans-serif" }}>{g.title}</div>
                  <div style={{ fontSize: 12, color: "#666", marginTop: 3, fontFamily: "'Sora', sans-serif" }}>{g.desc}</div>
                </button>
              ))}
            </div>
          </>
        )}

        {/* STEP 2 */}
        {formStep === 2 && (
          <>
            <SectionTitle step={2} title="ABOUT YOU" sub="Helps us calculate exact calorie & nutrition targets." />
            <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 10, marginBottom: 12 }}>
              <div>
                <label style={S.label}>Age</label>
                <input type="number" value={form.age} onChange={e => set("age", e.target.value)} placeholder="25" style={S.input} />
              </div>
              <div>
                <label style={S.label}>Sex</label>
                <div style={{ display: "flex", gap: 8 }}>
                  {["Male", "Female"].map(s => (
                    <button key={s} onClick={() => set("sex", s)} style={{
                      flex: 1, padding: "14px", borderRadius: 12, cursor: "pointer",
                      border: form.sex === s ? "2px solid #B5FF47" : "2px solid rgba(255,255,255,0.1)",
                      background: form.sex === s ? "rgba(181,255,71,0.1)" : "rgba(255,255,255,0.04)",
                      color: form.sex === s ? "#B5FF47" : "#aaa", fontWeight: 700, fontSize: 14, fontFamily: "'Sora', sans-serif",
                    }}>{s}</button>
                  ))}
                </div>
              </div>
            </div>
            <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 10, marginBottom: 16 }}>
              <div>
                <label style={S.label}>Weight (kg)</label>
                <input type="number" value={form.weight} onChange={e => set("weight", e.target.value)} placeholder="75" style={S.input} />
              </div>
              <div>
                <label style={S.label}>Height (cm)</label>
                <input type="number" value={form.height} onChange={e => set("height", e.target.value)} placeholder="178" style={S.input} />
              </div>
            </div>
            <label style={S.label}>Experience Level</label>
            <ChoiceBtn selected={form.experience === "Complete beginner"} onClick={() => set("experience", "Complete beginner")} icon="🌱" title="Complete Beginner" sub="Never really trained before" />
            <ChoiceBtn selected={form.experience === "Some experience"} onClick={() => set("experience", "Some experience")} icon="🔄" title="Some Experience" sub="Trained on and off" />
            <ChoiceBtn selected={form.experience === "Intermediate"} onClick={() => set("experience", "Intermediate")} icon="💪" title="Intermediate" sub="Consistent 6+ months" />
          </>
        )}

        {/* STEP 3 */}
        {formStep === 3 && (
          <>
            <SectionTitle step={3} title="YOUR SETUP" sub="We'll build your plan around what you have available." />
            <label style={S.label}>Equipment Available</label>
            <ChoiceBtn selected={form.equipment === "Full gym"} onClick={() => set("equipment", "Full gym")} icon="🏋️" title="Full Gym" sub="Barbells, machines, cables, dumbbells" />
            <ChoiceBtn selected={form.equipment === "Dumbbells only"} onClick={() => set("equipment", "Dumbbells only")} icon="🎯" title="Dumbbells Only" sub="Adjustable or fixed dumbbells" />
            <ChoiceBtn selected={form.equipment === "No equipment"} onClick={() => set("equipment", "No equipment")} icon="🏠" title="Home / No Equipment" sub="Bodyweight only" />
            <label style={{ ...S.label, marginTop: 20 }}>Days Per Week</label>
            <div style={{ display: "flex", gap: 8 }}>
              {["2", "3", "4", "5", "6"].map(d => (
                <button key={d} onClick={() => set("days", d)} style={{
                  flex: 1, padding: "14px 0", borderRadius: 12, cursor: "pointer",
                  border: form.days === d ? "2px solid #B5FF47" : "2px solid rgba(255,255,255,0.1)",
                  background: form.days === d ? "rgba(181,255,71,0.1)" : "rgba(255,255,255,0.04)",
                  color: form.days === d ? "#B5FF47" : "#aaa", fontWeight: 800, fontSize: 18, fontFamily: "'Sora', sans-serif",
                }}>{d}</button>
              ))}
            </div>
          </>
        )}

        {/* STEP 4 */}
        {formStep === 4 && (
          <>
            <SectionTitle step={4} title="YOUR DIET" sub="Select all that apply — we'll tailor your meal plan." />
            {errorMsg && (
              <div style={{ background: "rgba(255,80,80,0.1)", border: "1px solid rgba(255,80,80,0.3)", borderRadius: 10, padding: "12px 16px", marginBottom: 20, fontSize: 14, color: "#ff8080" }}>
                {errorMsg}
              </div>
            )}
            <div style={{ display: "flex", flexWrap: "wrap", gap: 8, marginBottom: 24 }}>
              {DIET_TAGS.map(tag => (
                <button key={tag} onClick={() => toggleDiet(tag)} style={{
                  padding: "10px 18px", borderRadius: 50, cursor: "pointer", fontSize: 13, fontWeight: 600, fontFamily: "'Sora', sans-serif", transition: "all 0.2s",
                  border: form.diet.includes(tag) ? "2px solid #B5FF47" : "2px solid rgba(255,255,255,0.12)",
                  background: form.diet.includes(tag) ? "rgba(181,255,71,0.1)" : "transparent",
                  color: form.diet.includes(tag) ? "#B5FF47" : "#aaa",
                }}>{tag}</button>
              ))}
            </div>
            <label style={S.label}>Allergies or Extra Notes (optional)</label>
            <textarea value={form.notes} onChange={e => set("notes", e.target.value)} placeholder="e.g. nut allergy, bad knees, work night shifts..." style={{ ...S.input, minHeight: 90, resize: "vertical" }} />
          </>
        )}

        <div style={{ marginTop: 28 }}>
          {formStep < 4
            ? <button onClick={() => setFormStep(s => s + 1)} disabled={!canNext()} style={{ ...S.btn, opacity: canNext() ? 1 : 0.3, cursor: canNext() ? "pointer" : "not-allowed" }}>Continue →</button>
            : <button onClick={generate} style={S.btn}>✦ Generate My Plan</button>
          }
        </div>
      </div>
    </div>
  );

  // ── PLAN ─────────────────────────────────────────────────────────────────
  if (screen === "plan" && plan) return (
    <div style={S.app}>
      <link href="https://fonts.googleapis.com/css2?family=Sora:wght@400;600;700;800&family=Bebas+Neue&display=swap" rel="stylesheet" />
      <Blob style={{ width: 500, height: 500, background: "#B5FF47", top: -200, right: -200 }} />

      {/* Hero header */}
      <div style={{ background: "rgba(181,255,71,0.04)", borderBottom: "1px solid rgba(181,255,71,0.1)", padding: "24px 22px" }}>
        <div style={{ maxWidth: 520, margin: "0 auto" }}>
          <div style={{ display: "flex", justifyContent: "space-between", alignItems: "flex-start" }}>
            <div>
              <div style={{ fontSize: 11, color: "#B5FF47", fontWeight: 700, letterSpacing: "0.1em", marginBottom: 4 }}>YOUR PLAN IS READY</div>
              <h2 style={{ fontFamily: "'Bebas Neue', sans-serif", fontSize: 38, margin: 0, letterSpacing: 1 }}>{plan.name}</h2>
            </div>
            <button onClick={() => { setScreen("landing"); }} style={S.ghost}>Start Over</button>
          </div>
          <p style={{ color: "#888", fontSize: 14, margin: "12px 0 0", lineHeight: 1.6 }}>{plan.summary}</p>
        </div>
      </div>

      <div style={{ ...S.wrap, paddingTop: 24 }}>
        {/* Macros */}
        <div style={{ display: "flex", gap: 8, marginBottom: 24 }}>
          <div style={{ textAlign: "center", background: "rgba(181,255,71,0.08)", border: "1px solid rgba(181,255,71,0.18)", borderRadius: 14, padding: "16px 10px", flex: 1.2 }}>
            <div style={{ fontSize: 22, fontWeight: 800, color: "#B5FF47", fontFamily: "'Sora', sans-serif" }}>{plan.calories}<span style={{ fontSize: 11, fontWeight: 500 }}> kcal</span></div>
            <div style={{ fontSize: 10, color: "#666", marginTop: 3, fontWeight: 700, textTransform: "uppercase", letterSpacing: "0.08em" }}>Daily Target</div>
          </div>
          {[{ l: "Protein", v: plan.protein, c: "#60A5FA" }, { l: "Carbs", v: plan.carbs, c: "#F472B6" }, { l: "Fat", v: plan.fat, c: "#FBBF24" }].map(m => (
            <div key={m.l} style={{ textAlign: "center", background: "rgba(255,255,255,0.04)", borderRadius: 14, padding: "16px 8px", flex: 1 }}>
              <div style={{ fontSize: 20, fontWeight: 800, color: m.c, fontFamily: "'Sora', sans-serif" }}>{m.v}<span style={{ fontSize: 10, color: "#555" }}>g</span></div>
              <div style={{ fontSize: 10, color: "#555", marginTop: 3, fontWeight: 700, textTransform: "uppercase", letterSpacing: "0.08em" }}>{m.l}</div>
            </div>
          ))}
        </div>

        {/* Tabs */}
        <div style={{ display: "flex", background: "rgba(255,255,255,0.04)", borderRadius: 12, padding: 4, marginBottom: 24 }}>
          {["workout", "nutrition", "tips"].map(tab => (
            <button key={tab} onClick={() => setPlanTab(tab)} style={{
              flex: 1, padding: "10px", borderRadius: 9, border: "none", cursor: "pointer",
              background: planTab === tab ? "#B5FF47" : "transparent",
              color: planTab === tab ? "#0b0b0b" : "#666",
              fontWeight: 700, fontSize: 13, fontFamily: "'Sora', sans-serif", transition: "all 0.2s",
            }}>
              {tab === "workout" ? "💪 Workout" : tab === "nutrition" ? "🥗 Nutrition" : "💡 Tips"}
            </button>
          ))}
        </div>

        {/* WORKOUT */}
        {planTab === "workout" && (
          <div>
            <div style={{ fontSize: 12, color: "#444", marginBottom: 14, fontWeight: 600 }}>
              REST DAYS: {plan.restDays?.join(", ")}
            </div>
            {plan.workoutDays?.map((day, i) => (
              <div key={i} style={{ marginBottom: 8, background: "rgba(255,255,255,0.03)", border: "1px solid rgba(255,255,255,0.07)", borderRadius: 14, overflow: "hidden" }}>
                <button onClick={() => setOpenDay(openDay === i ? -1 : i)} style={{
                  width: "100%", display: "flex", justifyContent: "space-between", alignItems: "center",
                  padding: "16px 18px", background: "none", border: "none", color: "#fff", cursor: "pointer",
                }}>
                  <div style={{ fontWeight: 700, fontSize: 15, fontFamily: "'Sora', sans-serif" }}>{day.day}</div>
                  <div style={{ display: "flex", alignItems: "center", gap: 10 }}>
                    <span style={{ fontSize: 12, color: "#444" }}>{day.exercises?.length} exercises</span>
                    <span style={{ color: "#B5FF47", fontSize: 16, display: "inline-block", transform: openDay === i ? "rotate(180deg)" : "rotate(0)", transition: "transform 0.2s" }}>▾</span>
                  </div>
                </button>
                {openDay === i && (
                  <div style={{ padding: "0 18px 16px" }}>
                    {day.exercises?.map((ex, j) => (
                      <div key={j} style={{ display: "flex", justifyContent: "space-between", padding: "11px 0", borderBottom: j < day.exercises.length - 1 ? "1px solid rgba(255,255,255,0.05)" : "none" }}>
                        <div style={{ flex: 1, paddingRight: 12 }}>
                          <div style={{ fontWeight: 600, fontSize: 14, fontFamily: "'Sora', sans-serif" }}>{ex.name}</div>
                          {ex.note && <div style={{ fontSize: 12, color: "#666", marginTop: 3, lineHeight: 1.4, fontFamily: "'Sora', sans-serif" }}>💡 {ex.note}</div>}
                        </div>
                        <div style={{ textAlign: "right", flexShrink: 0 }}>
                          <div style={{ fontSize: 13, fontWeight: 700, color: "#B5FF47", fontFamily: "'Sora', sans-serif" }}>{ex.sets}</div>
                          <div style={{ fontSize: 11, color: "#444", marginTop: 2, fontFamily: "'Sora', sans-serif" }}>{ex.rest}</div>
                        </div>
                      </div>
                    ))}
                  </div>
                )}
              </div>
            ))}
          </div>
        )}

        {/* NUTRITION */}
        {planTab === "nutrition" && (
          <div>
            {plan.meals?.map((m, i) => (
              <div key={i} style={{ background: "rgba(255,255,255,0.03)", border: "1px solid rgba(255,255,255,0.07)", borderRadius: 14, padding: "16px 18px", marginBottom: 10 }}>
                <div style={{ display: "flex", justifyContent: "space-between", alignItems: "center", marginBottom: 12 }}>
                  <div style={{ fontWeight: 700, fontSize: 15, fontFamily: "'Sora', sans-serif" }}>{m.meal}</div>
                  <div style={{ fontSize: 13, fontWeight: 700, color: "#B5FF47", fontFamily: "'Sora', sans-serif" }}>{m.calories} kcal</div>
                </div>
                <div style={{ display: "flex", flexWrap: "wrap", gap: 6 }}>
                  {m.foods?.map((f, j) => (
                    <span key={j} style={{ fontSize: 12, background: "rgba(181,255,71,0.08)", border: "1px solid rgba(181,255,71,0.15)", color: "#B5FF47", padding: "5px 12px", borderRadius: 20, fontFamily: "'Sora', sans-serif" }}>{f}</span>
                  ))}
                </div>
              </div>
            ))}
            <div style={{ background: "rgba(96,165,250,0.07)", border: "1px solid rgba(96,165,250,0.18)", borderRadius: 14, padding: "14px 16px", marginTop: 4, fontSize: 13, color: "#93C5FD", lineHeight: 1.5 }}>
              💧 Aim for 2.5–3 litres of water daily. More on training days.
            </div>
          </div>
        )}

        {/* TIPS */}
        {planTab === "tips" && (
          <div>
            {plan.tips?.map((tip, i) => (
              <div key={i} style={{ display: "flex", gap: 14, padding: "16px 18px", background: "rgba(255,255,255,0.03)", border: "1px solid rgba(255,255,255,0.07)", borderRadius: 14, marginBottom: 10 }}>
                <div style={{ fontSize: 22, flexShrink: 0 }}>{["🎯", "🛌", "📈", "✅"][i] || "✅"}</div>
                <p style={{ margin: 0, fontSize: 14, color: "#ccc", lineHeight: 1.6, fontFamily: "'Sora', sans-serif" }}>{tip}</p>
              </div>
            ))}
            <div style={{ background: "rgba(181,255,71,0.06)", border: "1px solid rgba(181,255,71,0.14)", borderRadius: 14, padding: "16px 18px", marginTop: 4 }}>
              <div style={{ fontWeight: 700, fontSize: 14, color: "#B5FF47", marginBottom: 6, fontFamily: "'Sora', sans-serif" }}>🔁 Stick to the plan for 8 weeks</div>
              <p style={{ margin: 0, fontSize: 13, color: "#777", lineHeight: 1.6, fontFamily: "'Sora', sans-serif" }}>Consistency beats perfection. Show up, follow the plan, and adjust after 8 weeks based on your results.</p>
            </div>
          </div>
        )}
      </div>
    </div>
  );

  return null;
}
