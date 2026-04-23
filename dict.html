import { useState, useCallback } from "react";

const API_KEY = "7DF19D1F143E119E6B0356A45BEAEEEE";
const KRDICT_URL = "https://krdict.korean.go.kr/api/search";

function detectLang(text) {
  if (/[\uAC00-\uD7A3\u1100-\u11FF\u3130-\u318F]/.test(text)) return "korean";
  if (/[\u0E00-\u0E7F]/.test(text)) return "thai";
  return "english";
}

async function fetchKrDict(query) {
  const params = new URLSearchParams({ key: API_KEY, q: query, part: "word", sort: "popular", num: "5", lang: "en" });
  const res = await fetch(`${KRDICT_URL}?${params}`);
  const text = await res.text();
  const xml = new DOMParser().parseFromString(text, "text/xml");
  return Array.from(xml.querySelectorAll("item")).map(item => ({
    word: item.querySelector("word")?.textContent || "",
    pos: item.querySelector("pos")?.textContent || "",
    definitions: Array.from(item.querySelectorAll("sense")).map(s => s.querySelector("definition")?.textContent || "").filter(Boolean),
    origin: item.querySelector("origin")?.textContent || "",
    pronunciation: item.querySelector("pronunciation")?.textContent || "",
  })).filter(d => d.word);
}

function buildPrompt(word, detectedLang, baseInfo) {
  const isKorean = detectedLang === "korean";
  const sourceLang = detectedLang === "thai" ? "Thai" : detectedLang === "english" ? "English" : "Korean";
  return isKorean ? `You are a Korean-English-Thai linguistics expert referencing 한국어기초사전.
Word: "${word}" (Korean)${baseInfo ? `\nKrdict:\n${baseInfo}` : ""}
Return ONLY compact JSON, no markdown:
{"korean_word":"...","pos_ko":"...","pronunciation_ko":"...","origin":"...","translations":[{"english_word":"...","definition_en":"...","thai_word":"...","definition_th":"...","definition_ko":"...","synonyms":[],"antonyms":[]}]}
Rules: pos_ko=명사/동사 etc; pronunciation_ko=한글 phonetic no brackets; origin=Hanja chars or empty; up to 3 meanings; all definition fields required.`
  : `You are a Korean-English-Thai linguistics expert referencing 한국어기초사전.
Word: "${word}" (${sourceLang})
Return ONLY compact JSON, no markdown:
{"input_word":"...","input_def":"...","results":[{"korean_word":"...","pos_ko":"...","pronunciation_ko":"...","origin":"...","definition_ko":"...","synonyms":[],"antonyms":[]}]}
Rules: input_def in source language; up to 4 Korean equivalents; pos_ko=명사/동사 etc; pronunciation_ko=한글 no brackets; origin=Hanja or empty; definition_ko required.`;
}

async function fetchClaude(word, detectedLang, krdictData, retries = 2) {
  const baseInfo = krdictData.length
    ? krdictData.map((d, i) => `${i + 1}.[${d.pos}]${d.definitions[0]}`).join(";")
    : "";
  const prompt = buildPrompt(word, detectedLang, baseInfo);

  for (let attempt = 0; attempt <= retries; attempt++) {
    try {
      const controller = new AbortController();
      const timer = setTimeout(() => controller.abort(), 25000);
      const res = await fetch("https://api.anthropic.com/v1/messages", {
        method: "POST",
        signal: controller.signal,
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ model: "claude-haiku-4-5-20251001", max_tokens: 1000, messages: [{ role: "user", content: prompt }] }),
      });
      clearTimeout(timer);
      if (!res.ok) throw new Error(`HTTP ${res.status}`);
      const data = await res.json();
      const raw = data.content?.map(b => b.text || "").join("") || "";
      const clean = raw.replace(/```json|```/g, "").trim();
      const parsed = JSON.parse(clean);
      return { lang: detectedLang, ...parsed };
    } catch (e) {
      if (attempt === retries) throw e;
      await new Promise(r => setTimeout(r, 1000 * (attempt + 1)));
    }
  }
}

function Spinner() {
  return <div className="flex justify-center py-12"><div className="w-7 h-7 border-2 border-stone-200 border-t-stone-400 rounded-full animate-spin" /></div>;
}

function TitleCard({ result }) {
  return (
    <div className="bg-white rounded-2xl shadow-sm px-5 py-4">
      <div className="flex items-center gap-2 flex-wrap">
        <span className="text-2xl font-bold text-stone-800">{result.korean_word}</span>
        {result.pos_ko && (
          <span className="text-xs bg-rose-50 text-rose-400 px-2 py-0.5 rounded-md font-medium">{result.pos_ko}</span>
        )}
        {result.pronunciation_ko && (
          <span className="text-stone-400 text-sm">({result.pronunciation_ko})</span>
        )}
        {result.origin && (
          <span className="text-stone-400 text-sm">({result.origin})</span>
        )}
      </div>
    </div>
  );
}

function KoreanDefCard({ t, i }) {
  return (
    <div className="bg-white rounded-2xl shadow-sm px-5 py-5">
      <div className="flex items-center gap-2 flex-wrap mb-4">
        <span className="text-lg font-bold text-stone-800">{t.korean_word || "—"}</span>
        {t.pos_ko && <span className="text-xs bg-rose-50 text-rose-400 px-2 py-0.5 rounded-md font-medium">{t.pos_ko}</span>}
        {t.pronunciation_ko && <span className="text-stone-400 text-sm">({t.pronunciation_ko})</span>}
        {t.origin && <span className="text-stone-400 text-sm">({t.origin})</span>}
      </div>
      <p className="text-stone-400 text-sm font-medium mb-3">({i + 1})</p>

      {/* 🇰🇷 Korean def */}
      <div className="flex gap-3 mb-4">
        <span className="text-lg mt-0.5">🇰🇷</span>
        <p className="text-sm text-stone-600 leading-relaxed">{t.definition_ko}</p>
      </div>

      {/* 🇬🇧 English */}
      <div className="flex gap-3 mb-4">
        <span className="text-lg mt-0.5">🇬🇧</span>
        <div>
          <p className="font-semibold text-stone-800">{t.english_word}</p>
          {t.definition_en && <p className="text-sm text-stone-500 mt-1 leading-relaxed">⤷ {t.definition_en}</p>}
        </div>
      </div>

      {/* 🇹🇭 Thai */}
      <div className="flex gap-3 mb-4">
        <span className="text-lg mt-0.5">🇹🇭</span>
        <div>
          <p className="font-semibold text-stone-800">{t.thai_word}</p>
          {t.definition_th && <p className="text-sm text-stone-500 mt-1 leading-relaxed">⤷ {t.definition_th}</p>}
        </div>
      </div>

      {t.synonyms?.length > 0 && (
        <div className="flex flex-wrap gap-1 items-center mb-2">
          <span className="text-xs text-stone-400 mr-1">유의어</span>
          {t.synonyms.map((s, j) => <span key={j} className="text-xs bg-green-50 text-green-600 px-2 py-0.5 rounded-full">{s}</span>)}
        </div>
      )}
      {t.antonyms?.length > 0 && (
        <div className="flex flex-wrap gap-1 items-center">
          <span className="text-xs text-stone-400 mr-1">반의어</span>
          {t.antonyms.map((a, j) => <span key={j} className="text-xs bg-rose-50 text-rose-500 px-2 py-0.5 rounded-full">{a}</span>)}
        </div>
      )}
    </div>
  );
}

function EnThResultCard({ r, flag, inputDef, inputWord, showInputHeader, lang }) {
  return (
    <div className="bg-white rounded-2xl shadow-sm px-5 py-5">
      {showInputHeader && (
        <div className="flex gap-3 mb-4">
          <span className="text-lg mt-0.5">{flag}</span>
          <div>
            <p className="font-semibold text-stone-800">{inputWord}</p>
            {inputDef && <p className="text-sm text-stone-500 mt-1 leading-relaxed">{inputDef}</p>}
          </div>
        </div>
      )}

      {/* 🇰🇷 Korean word + def */}
      <div className="flex gap-3 mb-4">
        <span className="text-lg mt-0.5">🇰🇷</span>
        <div>
          <div className="flex items-center gap-2 flex-wrap mb-1">
            <p className="font-semibold text-stone-800">{r.korean_word}</p>
            {r.pos_ko && <span className="text-xs bg-rose-50 text-rose-400 px-2 py-0.5 rounded-md font-medium">{r.pos_ko}</span>}
            {r.pronunciation_ko && <span className="text-stone-400 text-xs">({r.pronunciation_ko})</span>}
            {r.origin && <span className="text-stone-400 text-xs">({r.origin})</span>}
          </div>
          {r.definition_ko && <p className="text-sm text-stone-500 mt-1 leading-relaxed">⤷ {r.definition_ko}</p>}
        </div>
      </div>

      {r.synonyms?.length > 0 && (
        <div className="flex flex-wrap gap-1 items-center mb-2">
          <span className="text-xs text-stone-400 mr-1">유의어</span>
          {r.synonyms.map((s, j) => <span key={j} className="text-xs bg-green-50 text-green-600 px-2 py-0.5 rounded-full">{s}</span>)}
        </div>
      )}
      {r.antonyms?.length > 0 && (
        <div className="flex flex-wrap gap-1 items-center">
          <span className="text-xs text-stone-400 mr-1">반의어</span>
          {r.antonyms.map((a, j) => <span key={j} className="text-xs bg-rose-50 text-rose-500 px-2 py-0.5 rounded-full">{a}</span>)}
        </div>
      )}
    </div>
  );
}

export default function App() {
  const [query, setQuery] = useState("");
  const [result, setResult] = useState(null);
  const [loading, setLoading] = useState(false);
  const [loadingMsg, setLoadingMsg] = useState("");
  const [error, setError] = useState("");
  const [searched, setSearched] = useState(false);

  const search = useCallback(async () => {
    const q = query.trim();
    if (!q) return;
    setLoading(true); setError(""); setResult(null); setSearched(true);
    setLoadingMsg("검색 중...");
    try {
      const lang = detectLang(q);
      let krdictData = [];
      if (lang === "korean") { try { krdictData = await fetchKrDict(q); } catch {} }
      setLoadingMsg("결과 불러오는 중...");
      const data = await fetchClaude(q, lang, krdictData);
      if (data) setResult(data);
      else setError("결과를 찾을 수 없습니다. 다시 시도해주세요.");
    } catch (e) {
      if (e.name === "AbortError") setError("시간이 초과되었습니다. 다시 시도해주세요.");
      else setError("오류가 발생했습니다. 다시 시도해주세요.");
    }
    finally { setLoading(false); setLoadingMsg(""); }
  }, [query]);

  const isKorean = result?.lang === "korean";
  const isThai = result?.lang === "thai";
  const flag = isThai ? "🇹🇭" : "🇬🇧";

  return (
    <div className="min-h-screen bg-stone-100 flex flex-col">
      <div className="px-5 pt-10 pb-4">
        <h1 className="text-3xl font-bold text-stone-700 text-center mb-6" style={{ fontFamily: "serif", letterSpacing: "0.05em" }}>
          하린의 사전
        </h1>
        <div className="flex gap-2">
          <input
            className="flex-1 bg-white border border-stone-200 rounded-2xl px-4 py-3 text-base outline-none focus:border-stone-400 transition"
            placeholder="단어를 입력하세요..."
            value={query}
            onChange={e => setQuery(e.target.value)}
            onKeyDown={e => e.key === "Enter" && search()}
          />
          <button onClick={search} className="bg-stone-200 text-stone-600 px-4 py-3 rounded-2xl hover:bg-stone-300 transition text-lg">🔍</button>
        </div>
      </div>

      <div className="flex-1 px-5 pb-8 space-y-3">
        {loading && <div className="flex flex-col items-center py-12 gap-3"><div className="w-7 h-7 border-2 border-stone-200 border-t-stone-400 rounded-full animate-spin" /><p className="text-sm text-stone-400">{loadingMsg}</p></div>}
        {error && <p className="text-sm text-red-400 text-center mt-6">{error}</p>}

        {result && !loading && (
          <>
            {isKorean ? (
              <>
                {/* Korean input: title card + per-definition cards */}
                <TitleCard result={result} />
                {result.translations?.map((t, i) => (
                  <KoreanDefCard key={i} t={t} i={i} />
                ))}
              </>
            ) : (
              <>
                {/* English/Thai input: input word header card + one card per Korean result */}
                <div className="bg-white rounded-2xl shadow-sm px-5 py-4">
                  <div className="flex gap-3">
                    <span className="text-lg mt-0.5">{flag}</span>
                    <div>
                      <p className="text-xl font-bold text-stone-800">{result.input_word}</p>
                      {result.input_def && <p className="text-sm text-stone-500 mt-1 leading-relaxed">{result.input_def}</p>}
                    </div>
                  </div>
                </div>
                {result.results?.map((r, i) => (
                  <EnThResultCard key={i} r={r} flag={flag} lang={result.lang} />
                ))}
              </>
            )}
          </>
        )}

        {!searched && !loading && (
          <div className="text-center py-20 text-stone-300 select-none">
            <p className="text-5xl mb-3">사전</p>
            <p className="text-sm">단어를 검색해보세요</p>
          </div>
        )}
      </div>
    </div>
  );
}
