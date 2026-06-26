import React, { useState, useEffect, useRef } from 'react';
import { 
  CheckCircle2, XCircle, Award, RotateCcw, Play, 
  ArrowRight, ArrowLeft, BookOpen, Clock, Pause, 
  Check, X, Trash2, User, History, PenTool, Home,
  ChevronRight, Target, LayoutGrid
} from 'lucide-react';

// --- [통합 문제 데이터베이스] ---
// chapter: 단원, topic: 비슷한 문제 분산용, difficulty: 하/중/상, point: 3/4/5
const database = [
  // --- 1과 ---
  { chapter: 1, topic: "인권특징", difficulty: "하", point: 3, question: "인권은 국가가 법으로 정하기 이전부터 주어지는 자연적 권리라는 뜻의 용어는?", options: ["실정권", "사회권", "자연권", "참정권", "청구권"], correctAnswerIndex: 2, explanation: "법 이전의 권리는 *자연권*, 하늘이 주었다는 의미는 *천부인권*입니다." },
  { chapter: 1, topic: "기본권자유", difficulty: "중", point: 4, question: "국가의 간섭을 받지 않을 소극적·방어적 권리로, 신체의 자유 등이 포함되는 것은?", options: ["평등권", "자유권", "사회권", "청구권", "참정권"], correctAnswerIndex: 1, explanation: "국가 개입을 배제하는 가장 오래된 소극적 권리는 *자유권*입니다." },
  { chapter: 1, topic: "기본권사회", difficulty: "중", point: 4, question: "국민이 국가에 인간다운 생활 보장을 요구하는 '사회권'에 해당하는 것은?", options: ["종교의 자유", "선거권", "교육을 받을 권리", "국가배상청구권", "재산권 보장"], correctAnswerIndex: 2, explanation: "교육, 근로, 쾌적한 환경 등 적극적으로 복지를 요구하는 권리는 *사회권*입니다." },
  { chapter: 1, topic: "기본권청구", difficulty: "상", point: 5, question: "다른 기본권 침해 시 이를 구제받기 위한 수단적·도구적 성격의 기본권은?", options: ["평등권", "청구권", "참정권", "사회권", "자유권"], correctAnswerIndex: 1, explanation: "기본권을 지키기 위한 수단적 권리는 *청구권*입니다." },
  { chapter: 1, topic: "기본권제한", difficulty: "상", point: 5, question: "헌법 제37조 2항의 기본권 제한 조건으로 올바른 것은?", options: ["대통령령으로 무제한 제한 가능", "국가안전보장을 위해 본질적 내용까지 제한 가능", "국가안전보장, 질서유지, 공공복리를 위해 법률로 제한하되 본질적 내용 침해 불가", "비상시 행정부 명령으로 즉시 제한", "어떤 상황에서도 기본권 제한은 불가능"], correctAnswerIndex: 2, explanation: "반드시 *법률*로 제한해야 하며, *본질적 내용*은 침해할 수 없습니다." },
  { chapter: 1, topic: "구제기관", difficulty: "중", point: 3, question: "행정 기관의 잘못된 처분으로 인한 고충 민원을 처리하는 국무총리 소속 기관은?", options: ["언론중재위원회", "국가인권위원회", "국민권익위원회", "헌법재판소", "한국소비자원"], correctAnswerIndex: 2, explanation: "행정 고충 처리는 *국민권익위원회* 담당입니다." },

  // --- 2과 ---
  { chapter: 2, topic: "국회기능", difficulty: "하", point: 3, question: "국민의 대표 기관으로서 법률을 제정·개정하는 입법 기관은?", options: ["행정부", "국회", "법원", "감사원", "헌법재판소"], correctAnswerIndex: 1, explanation: "입법을 담당하는 국민의 대표는 *국회*입니다." },
  { chapter: 2, topic: "국회견제", difficulty: "중", point: 4, question: "대통령이 체결한 중요 외교 조약에 대해 국회가 행사하는 통제 권한은?", options: ["조약 위헌 심판권", "조약 폐기권", "조약 체결·비준 동의권", "외교관 파면권", "조약 해석권"], correctAnswerIndex: 2, explanation: "국회는 국가 중대 조약에 대해 *동의권*을 행사합니다." },
  { chapter: 2, topic: "예산심의", difficulty: "중", point: 4, question: "국가 예산의 흐름을 올바르게 설명한 것은?", options: ["정부 편성 → 국회 심의/확정 → 정부 집행", "국회 편성 → 정부 심의 → 국회 집행", "감사원 편성 → 국회 확정 → 정부 집행", "대통령 편성 → 법원 심의 → 정부 집행", "정부 편성 → 헌재 심의 → 국회 집행"], correctAnswerIndex: 0, explanation: "예산은 *정부*가 짜고, *국회*가 심사하며, 쓴 뒤 결산도 *국회*가 합니다." },
  { chapter: 2, topic: "대통령지위", difficulty: "중", point: 3, question: "긴급명령권, 계엄선포권, 외교사절 접견 등은 대통령의 어떤 지위로서의 권한인가?", options: ["행정부 수반", "국가 원수", "입법부 수장", "사법부 견제자", "국무회의 의장"], correctAnswerIndex: 1, explanation: "국가를 대표하고 비상시 헌법을 수호하는 지위는 *국가 원수*입니다." },
  { chapter: 2, topic: "감사원", difficulty: "중", point: 4, question: "대통령 직속이지만 직무상 독립되어 세입·세출 결산과 공무원 감찰을 하는 곳은?", options: ["국무회의", "검찰", "감사원", "국정원", "국민권익위"], correctAnswerIndex: 2, explanation: "최고의 독립 감사 기구는 *감사원*입니다." },
  { chapter: 2, topic: "법원심급", difficulty: "하", point: 3, question: "사법부 최고 기관으로서 최종적인 3심 재판을 담당하는 곳은?", options: ["고등법원", "헌법재판소", "대법원", "지방법원", "행정법원"], correctAnswerIndex: 2, explanation: "우리나라 3심 제도의 최종 법원은 *대법원*입니다." },
  { chapter: 2, topic: "헌재기능", difficulty: "상", point: 5, question: "공권력 남용으로 기본권을 침해당한 국민이 직접 헌법재판소에 구제를 요청하는 제도는?", options: ["위헌법률심판", "권한쟁의심판", "헌법소원심판", "탄핵심판", "정당해산심판"], correctAnswerIndex: 2, explanation: "국민이 직접 기본권 구제를 청구하는 것은 *헌법소원심판*입니다." },

  // --- 3과 ---
  { chapter: 3, topic: "희소성", difficulty: "하", point: 3, question: "인간의 무한한 욕구에 비해 자원이 부족한 현상은?", options: ["효율성", "희소성", "편익", "기회비용", "매몰비용"], correctAnswerIndex: 1, explanation: "욕구 대비 자원 부족 현상을 *희소성*이라 합니다." },
  { chapter: 3, topic: "기회비용", difficulty: "중", point: 4, question: "선택으로 인해 포기한 대안 중 가장 가치가 큰 것을 의미하는 용어는?", options: ["한계비용", "평균비용", "매몰비용", "기회비용", "선택비용"], correctAnswerIndex: 3, explanation: "포기한 것 중 가장 아까운 최대 가치를 *기회비용*이라 합니다." },
  { chapter: 3, topic: "경제체제", difficulty: "상", point: 5, question: "계획 경제 체제의 한계점(단점)으로 가장 적절한 것은?", options: ["극심한 빈부 격차 발생", "이윤 추구로 인한 환경 오염", "근로 의욕 저하로 인한 경제적 비효율", "독과점 기업의 횡포", "지나친 시장 경쟁"], correctAnswerIndex: 2, explanation: "국가가 명령하는 계획 경제는 일한 만큼 받지 못해 *근로 의욕 저하*가 큰 단점입니다." },
  { chapter: 3, topic: "기본문제", difficulty: "중", point: 3, question: "생산된 가치를 '누구에게 어떻게 나눌 것인가'와 관련된 기본 경제 문제는?", options: ["무엇을 생산할 것인가", "얼마나 생산할 것인가", "어떻게 생산할 것인가", "언제 생산할 것인가", "누구를 위하여 생산할 것인가"], correctAnswerIndex: 4, explanation: "분배와 관련된 문제는 *누구를 위하여 생산할 것인가*입니다." },

  // --- 6과 ---
  { chapter: 6, topic: "국제특성", difficulty: "하", point: 3, question: "국제 사회를 이루는 가장 기본적이고 전통적인 행위 주체는?", options: ["다국적 기업", "UN", "주권 국가", "NGO", "개인"], correctAnswerIndex: 2, explanation: "독립된 주권을 가진 *국가*가 기본 단위입니다." },
  { chapter: 6, topic: "국제기구", difficulty: "상", point: 4, question: "각국 '정부'를 회원으로 하는 '정부 간 기구'가 아닌 것은?", options: ["UN", "IMF", "그린피스", "UNICEF", "UNESCO"], correctAnswerIndex: 2, explanation: "그린피스, 국경없는 의사회는 민간 중심의 *국제 비정부 기구(NGO)*입니다." },
  { chapter: 6, topic: "영토분쟁", difficulty: "중", point: 4, question: "중국이 고조선, 고구려, 발해를 자국 역사로 편입하려는 역사 왜곡 프로젝트는?", options: ["동북공정", "서북공정", "남해공정", "야스쿠니 참배", "사이버 분쟁"], correctAnswerIndex: 0, explanation: "고구려/발해 등 동북 3성 지역 역사 왜곡은 *동북공정*입니다." },
  { chapter: 6, topic: "독도", difficulty: "중", point: 3, question: "우리나라와 일본 간의 독도 문제에 대한 올바른 설명은?", options: ["분쟁 지역으로 공동 관리 중이다.", "일본은 1905년 불법 편입을 근거로 삼는다.", "국제사법재판소 판결에 따르기로 합의했다.", "역사적 근거가 부족해 대응이 어렵다.", "독도는 배타적 경제수역 내에 없다."], correctAnswerIndex: 1, explanation: "독도는 명백한 고유 영토이며, 일본은 1905년의 *불법 편입*을 억지 근거로 삼습니다." },
  { chapter: 6, topic: "기후협약", difficulty: "상", point: 5, question: "2015년 채택되어 지구 기온 상승 폭을 1.5℃ 이하로 제한하기로 한 기후 협약은?", options: ["교토 의정서", "파리 협정", "람사르 협약", "몬트리올 의정서", "제네바 협약"], correctAnswerIndex: 1, explanation: "195개 당사국이 합의한 신기후체제는 *파리 협정*입니다." },

  // --- 7과 ---
  { chapter: 7, topic: "인구1위", difficulty: "하", point: 3, question: "선생님 필기 기준, 2024년 세계 인구수 1위로 올라선 국가는?", options: ["중국", "인도", "미국", "인도네시아", "파키스탄"], correctAnswerIndex: 1, explanation: "2024년 기준 세계 인구 1위는 *인도*로 역전되었습니다." },
  { chapter: 7, topic: "인구이동", difficulty: "중", point: 4, question: "우리나라 1960년대 산업화 시기, 농촌에서 도시로 인구가 이동한 현상은?", options: ["귀농귀촌", "이촌향도", "젠트리피케이션", "두뇌유출", "국제이주"], correctAnswerIndex: 1, explanation: "일자리를 찾아 농촌을 떠나 도시로 가는 현상은 *이촌향도*입니다." },
  { chapter: 7, topic: "제노포비아", difficulty: "중", point: 4, question: "이주민 유입 지역에서 일어나는 현지인들의 '외국인(이방인) 혐오' 현상을 뜻하는 용어는?", options: ["제노포비아", "다문화주의", "동화주의", "노스탤지어", "오리엔탈리즘"], correctAnswerIndex: 0, explanation: "문화적 차이로 인한 외국인 혐오나 배척은 *제노포비아(Xenophobia)*입니다." },
  { chapter: 7, topic: "두뇌유출", difficulty: "상", point: 4, question: "나이지리아 등에서 의료진 등 전문 인력이 선진국으로 이동하여 겪는 부작용은?", options: ["인구 과밀", "두뇌 유출(Brain drain)", "환경 오염", "자원 고갈", "문화 충돌"], correctAnswerIndex: 1, explanation: "우수한 인력이 해외로 빠져나가 공공서비스가 악화되는 현상은 *두뇌 유출*입니다." },
  { chapter: 7, topic: "산업분류", difficulty: "중", point: 3, question: "학습지 필기 기준, 자연에서 직접 자원을 채취하는 농·어업 등은 몇 차 산업인가?", options: ["1차 산업", "2차 산업", "3차 산업", "4차 산업", "분류불가"], correctAnswerIndex: 0, explanation: "자연에서 직접 얻는 것은 *1차*, 가공(제조)은 *2차*, 서비스는 *3차*입니다." }
];

export default function App() {
  const [appState, setAppState] = useState('login'); 
  const [currentUser, setCurrentUser] = useState('');
  const [nameInput, setNameInput] = useState('');
  
  // Quiz Mode: 'mock' (실전 27문제), 'chapter' (단원 40문제)
  const [quizMode, setQuizMode] = useState(null); 
  const [examConfig, setExamConfig] = useState({ title: '', pointType: 'mixed' });
  const [questions, setQuestions] = useState([]);
  
  const [currentIndex, setCurrentIndex] = useState(0);
  const [selectedAnswers, setSelectedAnswers] = useState({});
  const [oxStates, setOxStates] = useState({});
  const [score, setScore] = useState(0);
  
  const [timeElapsed, setTimeElapsed] = useState(0);
  const [isTimerRunning, setIsTimerRunning] = useState(false);
  const timerRef = useRef(null);

  const [history, setHistory] = useState([]);
  const [userNotes, setUserNotes] = useState('');

  useEffect(() => {
    const saved = localStorage.getItem('master_quiz_user');
    if (saved) {
      setCurrentUser(saved);
      loadUserData(saved);
      setAppState('home');
    }
  }, []);

  const loadUserData = (username) => {
    setHistory(JSON.parse(localStorage.getItem(`hist_${username}`)) || []);
    setUserNotes(localStorage.getItem(`notes_${username}`) || '');
  };

  const handleLogin = (e) => {
    e.preventDefault();
    if (nameInput.trim()) {
      localStorage.setItem('master_quiz_user', nameInput.trim());
      setCurrentUser(nameInput.trim());
      loadUserData(nameInput.trim());
      setAppState('home');
    }
  };

  const logout = () => {
    localStorage.removeItem('master_quiz_user');
    setCurrentUser('');
    setAppState('login');
  };

  // --- [시험 생성 로직] ---
  // 1. 실전 모의고사 (27문제 / 1->2->3->6->7과 정렬 / 3,4,5점 배점)
  const startMockExam = () => {
    // 실제 운영 시에는 DB에서 랜덤으로 뽑지만, 구조상 챕터별로 정렬
    const sorted = [...database].sort((a, b) => a.chapter - b.chapter);
    // (예시에서는 확보된 DB를 모두 사용, 실제로는 27문제 맞춤)
    
    setExamConfig({ title: "실전 모의고사 (27문항)", pointType: 'mixed' });
    initExam(sorted);
  };

  // 2. 단원별 40문제 세트 (동일 주제 분산 알고리즘 / 2.5점 고정)
  const startChapterExam = (chapter, setNum) => {
    let pool = database.filter(q => q.chapter === chapter);
    // (예시를 위해 풀이 적으면 복제해서 40문항 테스트 환경 시뮬레이션)
    while(pool.length < 15 && pool.length > 0) { pool = [...pool, ...pool]; }
    pool = pool.slice(0, 40);

    // 비슷한 주제 분산 셔플 (간단한 버전)
    pool.sort(() => Math.random() - 0.5);
    for(let i=0; i<pool.length-1; i++) {
      if(pool[i].topic === pool[i+1].topic) {
        let swapIdx = i + 2;
        while(swapIdx < pool.length && pool[swapIdx].topic === pool[i].topic) swapIdx++;
        if(swapIdx < pool.length) {
          [pool[i+1], pool[swapIdx]] = [pool[swapIdx], pool[i+1]];
        }
      }
    }
    
    setExamConfig({ title: `${chapter}과 실전 세트 (${setNum})`, pointType: 'fixed' });
    initExam(pool);
  };

  const initExam = (qList) => {
    setQuestions(qList);
    setCurrentIndex(0);
    setSelectedAnswers({});
    setOxStates({});
    setScore(0);
    setTimeElapsed(0);
    setIsTimerRunning(true);
    setAppState('exam');
  };

  useEffect(() => {
    if (isTimerRunning) timerRef.current = setInterval(() => setTimeElapsed(p => p + 1), 1000);
    else clearInterval(timerRef.current);
    return () => clearInterval(timerRef.current);
  }, [isTimerRunning]);

  const fTime = (sec) => `${Math.floor(sec/60).toString().padStart(2,'0')}:${(sec%60).toString().padStart(2,'0')}`;

  const toggleOX = (qIdx, optIdx, mark, e) => {
    e.stopPropagation();
    const cur = oxStates[qIdx] || {};
    setOxStates({ ...oxStates, [qIdx]: { ...cur, [optIdx]: cur[optIdx] === mark ? null : mark } });
  };

  const submitExam = () => {
    setIsTimerRunning(false);
    let total = 0;
    questions.forEach((q, idx) => {
      if (selectedAnswers[idx] === q.correctAnswerIndex) {
        total += examConfig.pointType === 'fixed' ? 2.5 : q.point;
      }
    });
    // 보정 로직 (40문제가 안되는 샘플 테스트 환경 보정)
    if(questions.length < 40 && examConfig.pointType === 'fixed') {
        total = Math.round((total / (questions.length * 2.5)) * 100);
    }

    setScore(total);
    const rec = { id: Date.now(), title: examConfig.title, score: total, time: timeElapsed, date: new Date().toLocaleDateString() };
    const newHist = [rec, ...history];
    setHistory(newHist);
    localStorage.setItem(`hist_${currentUser}`, JSON.stringify(newHist));
    setAppState('result');
  };

  if (appState === 'login') return (
    <div className="min-h-screen bg-slate-900 flex items-center justify-center p-4">
      <div className="bg-white p-8 rounded-3xl w-full max-w-sm text-center shadow-2xl">
        <Target className="w-16 h-16 text-indigo-600 mx-auto mb-4" />
        <h1 className="text-2xl font-black mb-6">FINAL 모의고사</h1>
        <form onSubmit={handleLogin} className="space-y-4">
          <input value={nameInput} onChange={(e)=>setNameInput(e.target.value)} placeholder="이름을 입력하세요" className="w-full p-4 bg-slate-50 border rounded-xl text-center font-bold" required />
          <button className="w-full py-4 bg-indigo-600 text-white rounded-xl font-black text-lg">기록 연동하여 시작</button>
        </form>
      </div>
    </div>
  );

  return (
    <div className="min-h-screen bg-slate-100 flex flex-col font-sans">
      <header className="bg-white border-b sticky top-0 z-50">
        <div className="max-w-6xl mx-auto px-4 h-16 flex items-center justify-between">
          <div onClick={() => !isTimerRunning && setAppState('home')} className="flex items-center gap-2 cursor-pointer">
            <LayoutGrid className="w-6 h-6 text-indigo-600" />
            <span className="font-black text-lg">통합사회 <span className="text-indigo-600">MASTER</span></span>
          </div>
          <div className="flex gap-4 items-center font-bold text-sm">
            {appState === 'exam' ? (
              <span className="bg-indigo-50 text-indigo-600 px-4 py-1.5 rounded-full flex items-center gap-2 border border-indigo-100"><Clock className="w-4 h-4"/> {fTime(timeElapsed)}</span>
            ) : (
              <div className="flex gap-3 items-center">
                <span className="text-slate-600"><User className="w-4 h-4 inline mr-1"/>{currentUser}</span>
                <button onClick={logout} className="text-slate-400 hover:text-slate-600 bg-slate-50 px-2 py-1 rounded">로그아웃</button>
              </div>
            )}
          </div>
        </div>
      </header>

      <main className="flex-1 w-full max-w-6xl mx-auto p-4 py-6">
        {/* --- [HOME MENU] --- */}
        {appState === 'home' && (
          <div className="space-y-8 animate-in fade-in">
            {/* 실전 모의고사 배너 */}
            <div className="bg-indigo-900 rounded-[2rem] p-8 md:p-12 text-white shadow-2xl relative overflow-hidden flex flex-col md:flex-row items-center justify-between gap-8">
              <div className="z-10 relative">
                <span className="bg-indigo-500/40 border border-indigo-400 px-3 py-1 rounded-full text-xs font-black tracking-widest mb-4 inline-block">100% EXAM READY</span>
                <h2 className="text-3xl md:text-5xl font-black mb-4 leading-tight">실전 모음 모의고사</h2>
                <p className="text-indigo-200 mb-6 font-medium">1과 → 2과 → 3과 → 6과 → 7과 순서 출제.<br/>3점/4점/5점 배점 황금비율 (총 27문항, 100점 만점)</p>
                <button onClick={startMockExam} className="bg-white text-indigo-900 px-8 py-4 rounded-xl font-black text-lg hover:bg-indigo-50 active:scale-95 transition-all shadow-lg flex items-center gap-2">
                  <Play className="w-5 h-5 fill-current" /> 실전 모의고사 시작
                </button>
              </div>
              <Target className="w-48 h-48 text-indigo-800 absolute right-10 opacity-50" />
            </div>

            {/* 단원별 40문제 세트 메뉴 */}
            <div>
              <div className="flex items-center gap-2 mb-4 px-2">
                <BookOpen className="w-5 h-5 text-slate-700" />
                <h3 className="text-xl font-black text-slate-800">단원별 집중 훈련 <span className="text-sm font-bold text-slate-400 ml-2">(40문항 / 고정 2.5점)</span></h3>
              </div>
              <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-5 gap-4">
                {[
                  { ch: 1, title: '인권과 헌법', sets: [1,2,3], color: 'emerald' },
                  { ch: 2, title: '국가 기관', sets: [1,2,3], color: 'blue' },
                  { ch: 3, title: '경제 체제', sets: [1,2], color: 'amber' },
                  { ch: 6, title: '국제 정치', sets: [1,2,3], color: 'rose' },
                  { ch: 7, title: '인구 문제', sets: [1,2,3], color: 'purple' }
                ].map((chap) => (
                  <div key={chap.ch} className="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm hover:border-slate-300">
                    <h4 className={`font-black text-lg text-${chap.color}-600 mb-1`}>{chap.ch}과</h4>
                    <p className="text-xs font-bold text-slate-400 mb-4">{chap.title}</p>
                    <div className="space-y-2">
                      {chap.sets.map(s => (
                        <button key={s} onClick={() => startChapterExam(chap.ch, s)} className="w-full bg-slate-50 hover:bg-slate-100 text-slate-700 font-bold py-2 rounded-lg text-sm border border-slate-200 transition-colors flex justify-between px-4">
                          <span>세트 ({s})</span> <ChevronRight className="w-4 h-4 text-slate-400" />
                        </button>
                      ))}
                    </div>
                  </div>
                ))}
              </div>
            </div>

            {/* 하단 유틸 메뉴 */}
            <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div onClick={() => setAppState('history')} className="bg-white p-6 rounded-2xl border border-slate-200 flex items-center gap-4 cursor-pointer hover:shadow-md transition-all group">
                <div className="p-4 bg-slate-100 rounded-xl group-hover:bg-slate-800 group-hover:text-white transition-colors"><History className="w-6 h-6" /></div>
                <div><h3 className="font-black text-lg">내 기록 보기</h3><p className="text-sm text-slate-500 font-medium">채점 기록과 점수를 확인하세요.</p></div>
              </div>
              <div onClick={() => setAppState('notes')} className="bg-white p-6 rounded-2xl border border-slate-200 flex items-center gap-4 cursor-pointer hover:shadow-md transition-all group">
                <div className="p-4 bg-slate-100 rounded-xl group-hover:bg-slate-800 group-hover:text-white transition-colors"><PenTool className="w-6 h-6" /></div>
                <div><h3 className="font-black text-lg">암기 노트</h3><p className="text-sm text-slate-500 font-medium">외워야 할 내용을 요약해 두세요.</p></div>
              </div>
            </div>
          </div>
        )}

        {/* --- [EXAM VIEW] --- */}
        {appState === 'exam' && questions.length > 0 && (
          <div className="bg-white rounded-[2rem] shadow-xl border border-slate-200 flex flex-col min-h-[70vh] relative">
            {!isTimerRunning && (
              <div className="absolute inset-0 z-20 bg-slate-900/60 backdrop-blur-md flex flex-col items-center justify-center rounded-[2rem]">
                <div className="bg-white p-8 rounded-3xl text-center"><Pause className="w-12 h-12 text-slate-400 mx-auto mb-4"/>
                  <h3 className="text-2xl font-black mb-6">일시 정지</h3>
                  <button onClick={()=>setIsTimerRunning(true)} className="bg-indigo-600 text-white px-8 py-3 rounded-xl font-bold">계속 풀기</button>
                </div>
              </div>
            )}
            
            <div className="p-6 md:p-10 flex-1 flex flex-col">
              <div className="flex justify-between items-center mb-6">
                <span className="bg-slate-100 text-slate-500 font-black px-4 py-1.5 rounded-lg text-sm">Q. {currentIndex + 1} / {questions.length}</span>
                <div className="flex gap-2">
                  {examConfig.pointType === 'mixed' ? (
                    <span className="font-black px-3 py-1.5 bg-indigo-50 text-indigo-600 border border-indigo-100 rounded-lg text-xs">{questions[currentIndex].point}점</span>
                  ) : (
                    <span className="font-black px-3 py-1.5 bg-slate-100 text-slate-600 rounded-lg text-xs">2.5점</span>
                  )}
                  <span className={`font-black px-3 py-1.5 rounded-lg text-xs border ${
                    questions[currentIndex].difficulty === '상' ? 'bg-rose-50 text-rose-600 border-rose-100' :
                    questions[currentIndex].difficulty === '중' ? 'bg-blue-50 text-blue-600 border-blue-100' : 'bg-emerald-50 text-emerald-600 border-emerald-100'
                  }`}>난이도 {questions[currentIndex].difficulty}</span>
                </div>
                <button onClick={()=>setIsTimerRunning(false)} className="p-2 hover:bg-slate-100 rounded-lg"><Pause className="w-5 h-5 text-slate-400"/></button>
              </div>

              <h3 className="text-xl md:text-2xl font-bold text-slate-900 mb-8 leading-snug break-keep">{questions[currentIndex].question}</h3>

              <div className="space-y-3 mb-8">
                {questions[currentIndex].options.map((opt, i) => {
                  const ox = oxStates[currentIndex]?.[i];
                  const sel = selectedAnswers[currentIndex] === i;
                  return (
                    <div key={i} onClick={()=>setSelectedAnswers({...selectedAnswers, [currentIndex]:i})} className={`flex items-center p-4 border-2 rounded-2xl cursor-pointer transition-all active:scale-[0.99] select-none ${sel ? 'border-indigo-600 bg-indigo-50/40' : ox==='X' ? 'border-slate-100 bg-slate-50 opacity-50' : 'border-slate-200 hover:border-indigo-300'}`}>
                      <div className={`w-8 h-8 rounded-xl flex items-center justify-center font-black mr-4 shrink-0 ${sel ? 'bg-indigo-600 text-white' : 'bg-slate-100 text-slate-500'}`}>{i+1}</div>
                      <span className={`flex-1 font-bold text-[15px] md:text-base ${ox==='X' ? 'line-through text-slate-400' : 'text-slate-700'}`}>{opt}</span>
                      <div className="flex gap-1 border-l-2 pl-3 ml-2">
                        <button onClick={(e)=>toggleOX(currentIndex, i, 'O', e)} className={`w-10 h-10 rounded-xl font-black flex items-center justify-center transition-colors ${ox==='O' ? 'bg-emerald-500 text-white' : 'bg-slate-100 text-slate-400'}`}>O</button>
                        <button onClick={(e)=>toggleOX(currentIndex, i, 'X', e)} className={`w-10 h-10 rounded-xl font-black flex items-center justify-center transition-colors ${ox==='X' ? 'bg-rose-500 text-white' : 'bg-slate-100 text-slate-400'}`}>X</button>
                      </div>
                    </div>
                  )
                })}
              </div>

              <div className="mt-auto flex gap-4 pt-4 border-t-2 border-slate-100">
                <button onClick={handlePrev} disabled={currentIndex===0} className="px-6 py-4 border-2 border-slate-200 rounded-xl font-bold text-slate-500 disabled:opacity-30"><ArrowLeft className="w-5 h-5"/></button>
                {currentIndex === questions.length - 1 ? (
                  <button onClick={submitExam} disabled={Object.keys(selectedAnswers).length < questions.length} className="flex-1 bg-rose-600 text-white rounded-xl font-black text-lg disabled:opacity-50">제출하기</button>
                ) : (
                  <button onClick={handleNext} className="flex-1 bg-slate-900 text-white rounded-xl font-black flex justify-center items-center gap-2">다음 <ArrowRight className="w-5 h-5"/></button>
                )}
              </div>
            </div>
          </div>
        )}

        {/* --- [RESULT VIEW] --- */}
        {appState === 'result' && (
          <div className="animate-in fade-in">
            <div className="bg-white p-10 rounded-[2rem] border border-slate-200 text-center mb-8 shadow-sm">
              <h2 className="text-3xl font-black mb-4">{examConfig.title} 결과</h2>
              <div className="inline-block px-10 py-6 bg-slate-50 rounded-3xl border-2 border-slate-100 mb-4">
                <span className="text-6xl md:text-8xl font-black text-indigo-600">{score}</span><span className="text-2xl font-bold text-slate-400">/100</span>
              </div>
              <div className="flex justify-center gap-4 font-bold text-slate-500"><span className="bg-slate-100 px-4 py-2 rounded-lg">{fTime(timeElapsed)} 소요</span></div>
            </div>

            <div className="space-y-6 mb-10">
              {questions.map((q, idx) => {
                const uAns = selectedAnswers[idx];
                const isCor = uAns === q.correctAnswerIndex;
                return (
                  <div key={idx} className={`p-6 border-2 rounded-3xl ${isCor ? 'border-emerald-100 bg-white' : 'border-rose-100 bg-rose-50/40'}`}>
                    <div className="flex gap-4 mb-4">
                      <div className={`w-8 h-8 rounded-lg flex items-center justify-center font-black text-white shrink-0 ${isCor?'bg-emerald-500':'bg-rose-500'}`}>{idx+1}</div>
                      <div className="font-bold text-lg pt-0.5">{q.question}</div>
                    </div>
                    <div className="pl-12 space-y-2 mb-6 font-bold text-[15px]">
                      <p className={isCor ? 'text-emerald-600' : 'text-rose-500 line-through'}>내 선택: {uAns!==undefined ? q.options[uAns] : '미선택'}</p>
                      {!isCor && <p className="text-indigo-600">정답: {q.options[q.correctAnswerIndex]}</p>}
                    </div>
                    <div className="bg-slate-50 p-5 rounded-2xl ml-12 text-slate-600 font-medium">
                      <p className="text-xs font-black text-indigo-500 mb-2">해설</p>
                      {q.explanation.split(/(\*[^*]+\*)/g).map((p, i) => p.startsWith('*') ? <strong key={i} className="text-indigo-700 bg-indigo-100 px-1 py-0.5 rounded">{p.slice(1,-1)}</strong> : p)}
                    </div>
                  </div>
                )
              })}
            </div>
            <button onClick={()=>setAppState('home')} className="w-full py-5 bg-slate-900 text-white rounded-2xl font-black text-lg hover:bg-slate-800 flex justify-center items-center gap-2"><Home className="w-5 h-5"/> 메인으로</button>
          </div>
        )}

        {/* --- [HISTORY & NOTES VIEWS] --- */}
        {appState === 'history' && (
          <div className="animate-in fade-in">
            <button onClick={()=>setAppState('home')} className="mb-6 flex items-center gap-2 font-bold text-slate-500"><ArrowLeft className="w-5 h-5"/> 홈으로</button>
            <h2 className="text-3xl font-black mb-8">내 시험 기록</h2>
            <div className="grid gap-4">
              {history.map(h => (
                <div key={h.id} className="bg-white p-6 rounded-2xl border border-slate-200 flex justify-between items-center">
                  <div>
                    <h4 className="font-bold text-lg mb-1">{h.title}</h4>
                    <p className="text-sm font-bold text-slate-400">{h.date} · {fTime(h.time)} 소요</p>
                  </div>
                  <div className="flex items-center gap-6">
                    <div className="text-right"><span className="text-2xl font-black text-indigo-600">{h.score}</span><span className="font-bold text-slate-400">/100</span></div>
                    <button onClick={()=>{
                      const n = history.filter(x=>x.id!==h.id);
                      setHistory(n); localStorage.setItem(`hist_${currentUser}`, JSON.stringify(n));
                    }} className="p-3 bg-rose-50 text-rose-500 rounded-xl hover:bg-rose-500 hover:text-white transition-colors"><Trash2 className="w-5 h-5"/></button>
                  </div>
                </div>
              ))}
              {history.length===0 && <div className="text-center py-10 font-bold text-slate-400">기록이 없습니다.</div>}
            </div>
          </div>
        )}

        {appState === 'notes' && (
          <div className="animate-in fade-in flex flex-col h-[70vh]">
            <button onClick={()=>setAppState('home')} className="mb-6 flex items-center gap-2 font-bold text-slate-500"><ArrowLeft className="w-5 h-5"/> 홈으로</button>
            <h2 className="text-3xl font-black mb-6">암기 노트</h2>
            <textarea 
              value={userNotes} onChange={(e)=>{setUserNotes(e.target.value); localStorage.setItem(`notes_${currentUser}`, e.target.value)}}
              className="flex-1 w-full bg-white border border-slate-200 rounded-2xl p-6 font-medium text-slate-700 resize-none focus:outline-none focus:border-indigo-500"
              placeholder="자유롭게 요약하고 기록하세요. 자동 저장됩니다."
            />
          </div>
        )}
      </main>
    </div>
  );
}
