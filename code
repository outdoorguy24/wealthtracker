import React, { useState, useEffect, useMemo } from 'react';
import { 
  Plus, 
  Trash2, 
  ChevronLeft, 
  ChevronRight, 
  TrendingUp, 
  CreditCard, 
  PiggyBank, 
  Save,
  AlertCircle,
  CheckCircle2,
  FileText,
  Target,
  DollarSign,
  LayoutDashboard,
  PieChart as PieChartIcon,
  Image as ImageIcon,
  X,
  ArrowRight,
  Wallet,
  Building2,
  Calendar,
  BarChart3
} from 'lucide-react';
import { initializeApp } from 'firebase/app';
import { 
  getAuth, 
  signInAnonymously, 
  onAuthStateChanged,
  signInWithCustomToken
} from 'firebase/auth';
import { 
  getFirestore, 
  doc, 
  setDoc, 
  getDocs,
  collection,
  onSnapshot,
  query,
  orderBy
} from 'firebase/firestore';

// --- Firebase Configuration & Init ---
const firebaseConfig = {
  apiKey: "AIzaSyBwblWyDJKoN1oeV6Y8ZkLMbp01Coscgl0",
  authDomain: "my-wealth-tracker-af58d.firebaseapp.com",
  projectId: "my-wealth-tracker-af58d",
  storageBucket: "my-wealth-tracker-af58d.firebasestorage.app",
  messagingSenderId: "368343280988",
  appId: "1:368343280988:web:95cb956a26176868bde599",
  measurementId: "G-95HWDP8MFF"
};
const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);
const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';

// --- Helper Components ---

const Card = ({ children, className = "" }) => (
  <div className={`bg-white rounded-2xl shadow-sm border border-slate-100 overflow-hidden ${className}`}>
    {children}
  </div>
);

const StatCard = ({ icon: Icon, label, value, goal, goalLabel, colorClass, onChange, onGoalChange, subtext, readOnlyValue }) => {
  const percentage = goal > 0 ? Math.min((value / goal) * 100, 100) : 0;
  const isNegative = value < 0;
  
  return (
    <Card className="p-6 flex flex-col justify-between h-full transition-all duration-300 hover:shadow-md">
      <div className="flex items-start justify-between mb-4">
        <div className={`p-3 rounded-xl ${colorClass} bg-opacity-10`}>
          <Icon className={`w-6 h-6 ${colorClass.replace('bg-', 'text-')}`} />
        </div>
         <div className="text-xs font-medium text-slate-400 bg-slate-50 px-2 py-1 rounded-full">
           {readOnlyValue ? 'Calculated' : 'Editable'}
         </div>
      </div>
      
      <div>
        <p className="text-sm font-semibold text-slate-500 uppercase tracking-wide mb-1">{label}</p>
        <div className="relative group">
          <span className="absolute left-0 top-1/2 -translate-y-1/2 text-slate-400 font-medium">$</span>
          {readOnlyValue ? (
             <div className={`w-full text-3xl font-bold bg-transparent pl-6 py-1 ${isNegative ? 'text-rose-600' : 'text-slate-800'}`}>
                {value.toLocaleString(undefined, { minimumFractionDigits: 2, maximumFractionDigits: 2 })}
             </div>
          ) : (
            <input 
                type="number" 
                value={value} 
                onChange={(e) => onChange && onChange(parseFloat(e.target.value) || 0)}
                className="w-full text-3xl font-bold text-slate-800 bg-transparent border-b-2 border-transparent hover:border-slate-200 focus:border-indigo-500 focus:outline-none pl-6 py-1 transition-colors" 
            />
          )}
        </div>
        {subtext && <p className="text-xs text-slate-400 mt-1">{subtext}</p>}
      </div>

      <div className="mt-6">
        <div className="flex justify-between items-end mb-2">
          <div className="flex items-center gap-1 text-xs font-semibold text-slate-400 uppercase">
            <Target className="w-3 h-3" />
            <span>{goalLabel || "Target"}</span>
          </div>
          <div className="relative w-24">
             <span className="absolute left-0 top-1/2 -translate-y-1/2 text-slate-400 font-medium text-xs">$</span>
             <input 
                type="number" 
                value={goal} 
                onChange={(e) => onGoalChange && onGoalChange(parseFloat(e.target.value) || 0)}
                className="w-full text-right text-sm font-semibold text-slate-600 bg-slate-50 focus:bg-white rounded px-1 py-0.5 border border-transparent focus:border-indigo-300 focus:outline-none transition-all"
             />
          </div>
        </div>

        <div className="h-3 w-full bg-slate-100 rounded-full overflow-hidden">
          <div 
            className={`h-full rounded-full ${colorClass.replace('bg-', 'bg-')}`} 
            style={{ width: `${percentage}%`, transition: 'width 1s ease-in-out' }}
          />
        </div>
        <p className="text-xs text-slate-400 mt-2 text-right">
          {percentage.toFixed(0)}% Reached
        </p>
      </div>
    </Card>
  );
};

// --- Charting Components ---

const GroupedBarChart = ({ data, keys, colors }) => {
  const allValues = data.flatMap(d => Object.values(d.sources));
  const maxVal = Math.max(...allValues, 1000);

  return (
    <div className="flex flex-col h-full w-full">
      {/* Legend */}
      <div className="flex flex-wrap gap-4 mb-2 justify-end">
        {keys.map((key) => (
          <div key={key} className="flex items-center gap-2 text-xs">
             <div className="w-3 h-3 rounded-sm" style={{ backgroundColor: colors[key] || '#ccc' }} />
             <span className="text-slate-500 font-medium">{key}</span>
          </div>
        ))}
      </div>

      {/* Chart */}
      <div className="flex items-end justify-between h-48 gap-2 pt-4 relative w-full">
         {/* Grid Lines */}
         <div className="absolute inset-0 flex flex-col justify-between pointer-events-none opacity-30">
             <div className="w-full h-px bg-slate-300 border-t border-dashed"></div>
             <div className="w-full h-px bg-slate-300 border-t border-dashed"></div>
             <div className="w-full h-px bg-slate-300 border-t border-dashed"></div>
             <div className="w-full h-px bg-slate-300 border-t border-dashed"></div>
         </div>

         {data.map((item, i) => (
           <div key={i} className="relative flex-1 h-full flex flex-col justify-end group min-w-0">
              {/* Bar Group container */}
              <div className="flex items-end justify-center gap-[1px] w-full h-full">
                  {keys.map((key) => {
                    const val = item.sources[key] || 0;
                    const height = Math.max((val / maxVal) * 100, 0);
                    
                    return (
                      <div 
                        key={key}
                        className="w-full relative group/bar rounded-t-[2px] transition-all duration-500 hover:opacity-80 min-w-[2px]"
                        style={{ 
                          height: `${height}%`, 
                          backgroundColor: colors[key] || '#ccc' 
                        }}
                      >
                         {/* Tooltip */}
                         <div className="absolute bottom-full left-1/2 -translate-x-1/2 mb-1 opacity-0 group-hover/bar:opacity-100 transition-opacity bg-slate-800 text-white text-[10px] font-bold px-1.5 py-0.5 rounded pointer-events-none z-30 whitespace-nowrap shadow-sm">
                           ${val.toLocaleString()}
                         </div>
                      </div>
                    );
                  })}
              </div>
              
              <p className="mt-2 text-[10px] text-slate-400 font-medium uppercase w-full text-center truncate">
                {item.shortLabel}
              </p>
           </div>
         ))}
      </div>
      <div className="h-4"></div>
    </div>
  );
};

const SimpleBarChart = ({ data, onBarClick }) => {
  const maxVal = Math.max(...data.map(d => d.value), 1000);
  
  return (
    <div className="flex items-end justify-between h-48 gap-2 pt-8">
      {data.map((item, i) => {
        const height = Math.max((item.value / maxVal) * 100, 0);
        return (
          <div key={i} className="flex flex-col items-center flex-1 group cursor-pointer" onClick={() => onBarClick(item.label)}>
            <div className="relative w-full flex justify-center items-end h-full bg-slate-50 rounded-t-lg overflow-hidden hover:bg-indigo-50 transition-colors">
              <div 
                className="w-3/4 bg-emerald-400 rounded-t-sm transition-all duration-500 group-hover:bg-emerald-500"
                style={{ height: `${height}%` }}
              />
              <div className="absolute -top-8 opacity-0 group-hover:opacity-100 transition-opacity text-xs font-bold text-slate-600 bg-white shadow-sm px-2 py-1 rounded border border-slate-100 z-10 whitespace-nowrap">
                ${item.value.toLocaleString()}
              </div>
            </div>
            <p className="text-[10px] text-slate-400 mt-2 font-medium uppercase rotate-0 truncate w-full text-center">{item.shortLabel}</p>
          </div>
        )
      })}
    </div>
  );
};

const SimpleLineChart = ({ data }) => {
  const maxVal = Math.max(...data.map(d => d.value), 1);
  
  const getX = (index, length) => {
    if (length <= 1) return 50;
    const percentage = (index / (length - 1)) * 90 + 5; 
    return percentage;
  };

  const getY = (value, max) => {
    const normalized = value / max;
    const percentage = 100 - (normalized * 80 + 10); 
    return percentage;
  };

  const points = data.map((d, i) => {
    const x = getX(i, data.length);
    const y = getY(d.value, maxVal);
    return `${x},${y}`;
  }).join(' ');

  return (
    <div className="h-48 w-full relative pt-6 group">
      <div className="absolute inset-0 flex flex-col justify-between px-4 py-6 pointer-events-none">
        <div className="w-full h-px bg-slate-50"></div>
        <div className="w-full h-px bg-slate-50"></div>
        <div className="w-full h-px bg-slate-50"></div>
      </div>

      <svg className="w-full h-full overflow-visible" preserveAspectRatio="none" viewBox="0 0 100 100">
        <polyline 
          fill="none" 
          stroke="#f43f5e" 
          strokeWidth="2" 
          points={points} 
          vectorEffect="non-scaling-stroke"
          strokeLinecap="round"
          strokeLinejoin="round"
        />
      </svg>

      <div className="absolute inset-0 pointer-events-none">
        {data.map((d, i) => {
          const left = getX(i, data.length) + '%';
          const top = getY(d.value, maxVal) + '%';
          
          return (
            <div 
              key={i} 
              className="absolute w-3 h-3 bg-white border-2 border-rose-500 rounded-full shadow-sm hover:scale-150 hover:border-rose-600 transition-transform z-10 pointer-events-auto cursor-pointer flex items-center justify-center group/dot"
              style={{ left, top, transform: 'translate(-50%, -50%)' }}
            >
              <div className="absolute bottom-full mb-2 bg-slate-800 text-white text-xs font-bold px-2 py-1 rounded opacity-0 group-hover/dot:opacity-100 transition-opacity whitespace-nowrap">
                 ${d.value.toLocaleString()}
              </div>
            </div>
          );
        })}
      </div>

      <div className="absolute bottom-0 left-0 w-full flex justify-between px-[5%]">
        {data.map((d, i) => (
           <div key={i} className="text-[10px] text-slate-400 font-medium uppercase w-0 flex justify-center overflow-visible whitespace-nowrap">
              {d.shortLabel}
           </div>
        ))}
      </div>
    </div>
  );
};

const SimplePieChart = ({ data }) => {
  const total = data.reduce((acc, item) => acc + item.value, 0);
  let cumulativePercent = 0;

  function getCoordinatesForPercent(percent) {
    const x = Math.cos(2 * Math.PI * percent);
    const y = Math.sin(2 * Math.PI * percent);
    return [x, y];
  }

  return (
    <div className="flex items-center gap-8 h-48">
      <div className="h-40 w-40 relative flex-shrink-0">
        <svg viewBox="-1 -1 2 2" className="transform -rotate-90 w-full h-full">
           {data.map((slice, i) => {
             if (slice.value === 0) return null;
             const startPercent = cumulativePercent;
             const slicePercent = slice.value / total;
             cumulativePercent += slicePercent;
             const endPercent = cumulativePercent;

             const [startX, startY] = getCoordinatesForPercent(startPercent);
             const [endX, endY] = getCoordinatesForPercent(endPercent);
             
             if (slicePercent > 0.99) {
               return <circle key={i} cx="0" cy="0" r="1" fill={slice.color} />
             }

             const largeArcFlag = slicePercent > 0.5 ? 1 : 0;
             const pathData = [
               `M 0 0`,
               `L ${startX} ${startY}`,
               `A 1 1 0 ${largeArcFlag} 1 ${endX} ${endY}`,
               `Z`
             ].join(' ');

             return <path key={i} d={pathData} fill={slice.color} className="hover:opacity-80 transition-opacity" />;
           })}
           <circle cx="0" cy="0" r="0.6" fill="white" />
        </svg>
        <div className="absolute inset-0 flex items-center justify-center flex-col pointer-events-none">
           <span className="text-xs text-slate-400 font-bold uppercase">Total</span>
           <span className="text-sm font-bold text-slate-700">${total.toLocaleString()}</span>
        </div>
      </div>
      
      <div className="flex-1 overflow-y-auto max-h-full pr-2 space-y-2 custom-scrollbar">
        {data.map((item, i) => (
          <div key={i} className="flex items-center justify-between text-sm">
            <div className="flex items-center gap-2">
              <div className="w-3 h-3 rounded-full" style={{ backgroundColor: item.color }} />
              <span className="text-slate-600 font-medium truncate max-w-[100px]">{item.label}</span>
            </div>
            <span className="font-bold text-slate-700">${item.value.toLocaleString()}</span>
          </div>
        ))}
      </div>
    </div>
  );
};


// --- Sub-Views ---

const DashboardView = ({ user, financials, onNavigateToMonth }) => {
  const [monthlyData, setMonthlyData] = useState([]);
  const [dreamBoard, setDreamBoard] = useState([]);
  const [newImageLink, setNewImageLink] = useState('');
  const [isAddingImage, setIsAddingImage] = useState(false);

  useEffect(() => {
    if (!user) return;

    const monthsRef = collection(db, 'artifacts', appId, 'users', user.uid, 'months');
    const q = query(monthsRef);
    
    const unsubMonths = onSnapshot(q, (snapshot) => {
      const data = snapshot.docs.map(doc => ({
        id: doc.id,
        ...doc.data()
      })).sort((a, b) => a.id.localeCompare(b.id));
      setMonthlyData(data);
    });

    const dreamRef = doc(db, 'artifacts', appId, 'users', user.uid, 'settings', 'dreamBoard');
    const unsubDream = onSnapshot(dreamRef, (docSnap) => {
      if (docSnap.exists()) {
        setDreamBoard(docSnap.data().images || []);
      }
    });

    return () => {
      unsubMonths();
      unsubDream();
    };
  }, [user]);

  const addImage = async () => {
    if (!newImageLink) return;
    const newImages = [...dreamBoard, { id: Date.now(), url: newImageLink }];
    setDreamBoard(newImages);
    setNewImageLink('');
    setIsAddingImage(false);
    await setDoc(doc(db, 'artifacts', appId, 'users', user.uid, 'settings', 'dreamBoard'), { images: newImages });
  };

  const removeImage = async (id) => {
    const newImages = dreamBoard.filter(img => img.id !== id);
    setDreamBoard(newImages);
    await setDoc(doc(db, 'artifacts', appId, 'users', user.uid, 'settings', 'dreamBoard'), { images: newImages });
  };

  // -- Compute Chart Data --

  // 1. Savings (Net Income)
  const savingsData = monthlyData.map(m => {
    const totalIncome = m.incomeRows?.reduce((acc, r) => acc + (parseFloat(r.actual)||0), 0) || 0;
    const totalExpense = m.expenseRows?.reduce((acc, r) => acc + (parseFloat(r.actual)||0), 0) || 0;
    return {
      label: m.id,
      shortLabel: new Date(m.id + '-02').toLocaleDateString('en-US', { month: 'short' }),
      value: Math.max(totalIncome - totalExpense, 0)
    };
  }).slice(-12);

  // 2. Debt over time
  const debtData = monthlyData.map(m => {
    const totalDebt = m.debtRows?.reduce((acc, r) => acc + (parseFloat(r.balance)||0), 0) || 0;
    return {
      label: m.id,
      shortLabel: new Date(m.id + '-02').toLocaleDateString('en-US', { month: 'short' }),
      value: totalDebt
    };
  }).slice(-12);

  // 3. Spending Breakdown
  const expenseCategories = {};
  monthlyData.forEach(m => {
    m.expenseRows?.forEach(r => {
      const cat = r.category || 'Uncategorized';
      const val = parseFloat(r.actual) || 0;
      expenseCategories[cat] = (expenseCategories[cat] || 0) + val;
    });
  });
  
  const pieColors = ['#6366f1', '#ec4899', '#10b981', '#f59e0b', '#8b5cf6', '#0ea5e9'];
  const pieData = Object.entries(expenseCategories)
    .map(([label, value], i) => ({ label, value, color: pieColors[i % pieColors.length] }))
    .sort((a, b) => b.value - a.value)
    .slice(0, 6);

  // 4. Income Stacked Chart
  const incomeSourcesSet = new Set();
  monthlyData.forEach(m => {
    m.incomeRows?.forEach(r => {
      if (r.source) incomeSourcesSet.add(r.source);
    });
  });
  const incomeKeys = Array.from(incomeSourcesSet).sort(); // Consistent order
  
  // Assign consistent colors to income sources
  const incomeColors = {};
  const incomePalette = ['#3b82f6', '#8b5cf6', '#06b6d4', '#14b8a6', '#f97316', '#f43f5e'];
  incomeKeys.forEach((key, i) => {
    incomeColors[key] = incomePalette[i % incomePalette.length];
  });

  const incomeStackedData = monthlyData.map(m => {
    const sources = {};
    let total = 0;
    m.incomeRows?.forEach(r => {
      const val = parseFloat(r.actual) || 0;
      sources[r.source || 'Other'] = (sources[r.source || 'Other'] || 0) + val;
      total += val;
    });
    return {
      label: m.id,
      shortLabel: new Date(m.id + '-02').toLocaleDateString('en-US', { month: 'short' }),
      sources,
      total
    };
  }).slice(-12);

  return (
    <div className="space-y-8 animate-in fade-in duration-500">
      <div className="bg-gradient-to-r from-indigo-600 to-violet-600 rounded-3xl p-8 text-white shadow-lg relative overflow-hidden">
        <div className="relative z-10">
          <h1 className="text-3xl font-bold mb-2">Financial Overview</h1>
          <p className="text-indigo-100 opacity-90">Track your progress, analyze spending, and visualize your dreams.</p>
        </div>
        <div className="absolute right-0 top-0 w-64 h-64 bg-white opacity-5 rounded-full -mr-16 -mt-16 blur-3xl"></div>
        <div className="absolute left-0 bottom-0 w-48 h-48 bg-black opacity-10 rounded-full -ml-10 -mb-10 blur-2xl"></div>
      </div>

      {/* New Financial Summary Boxes */}
      <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
         <Card className="p-6 flex items-center gap-4">
            <div className="p-4 bg-indigo-100 text-indigo-600 rounded-full">
              <Wallet className="w-8 h-8" />
            </div>
            <div>
              <p className="text-xs text-slate-500 font-bold uppercase mb-1">Total Savings</p>
              <h3 className="text-3xl font-bold text-slate-800">${financials.currentSavings.toLocaleString()}</h3>
            </div>
         </Card>
         
         <Card className="p-6 flex items-center gap-4">
            <div className="p-4 bg-emerald-100 text-emerald-600 rounded-full">
              <TrendingUp className="w-8 h-8" />
            </div>
            <div>
              <p className="text-xs text-slate-500 font-bold uppercase mb-1">Net Worth</p>
              <h3 className="text-3xl font-bold text-slate-800">${financials.netWorth.toLocaleString()}</h3>
            </div>
         </Card>

         <Card className="p-6 flex items-center gap-4">
            <div className="p-4 bg-rose-100 text-rose-600 rounded-full">
              <CreditCard className="w-8 h-8" />
            </div>
            <div>
              <p className="text-xs text-slate-500 font-bold uppercase mb-1">Total Debt</p>
              <h3 className="text-3xl font-bold text-slate-800">${financials.totalDebt.toLocaleString()}</h3>
            </div>
         </Card>
      </div>

      <div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
        {/* Income Chart (New) */}
        <Card className="p-6 lg:col-span-2">
           <div className="flex items-center gap-2 mb-6">
            <div className="p-2 bg-blue-100 rounded-lg text-blue-600">
              <BarChart3 className="w-5 h-5" />
            </div>
            <h3 className="font-bold text-slate-700">Income Growth & Sources</h3>
          </div>
          {incomeStackedData.length > 0 ? (
            <GroupedBarChart data={incomeStackedData} keys={incomeKeys} colors={incomeColors} />
          ) : (
            <div className="h-48 flex items-center justify-center text-slate-400 text-sm">No income data yet</div>
          )}
        </Card>

        <Card className="p-6">
          <div className="flex items-center gap-2 mb-2">
            <div className="p-2 bg-emerald-100 rounded-lg text-emerald-600">
              <PiggyBank className="w-5 h-5" />
            </div>
            <h3 className="font-bold text-slate-700">Monthly Savings Added</h3>
          </div>
          {savingsData.length > 0 ? (
            <SimpleBarChart data={savingsData} onBarClick={onNavigateToMonth} />
          ) : (
            <div className="h-48 flex items-center justify-center text-slate-400 text-sm">No data yet</div>
          )}
        </Card>

        <Card className="p-6">
          <div className="flex items-center gap-2 mb-2">
            <div className="p-2 bg-rose-100 rounded-lg text-rose-600">
              <CreditCard className="w-5 h-5" />
            </div>
            <h3 className="font-bold text-slate-700">Debt Reduction</h3>
          </div>
           {debtData.some(d => d.value > 0) ? (
            <SimpleLineChart data={debtData} />
          ) : (
            <div className="h-48 flex items-center justify-center text-slate-400 text-sm text-center px-8">
              <p>No debt history recorded.<br/>Update your liabilities in the worksheet to see the trend.</p>
            </div>
          )}
        </Card>

        <Card className="p-6 lg:col-span-2">
           <div className="flex items-center gap-2 mb-6">
            <div className="p-2 bg-indigo-100 rounded-lg text-indigo-600">
              <PieChartIcon className="w-5 h-5" />
            </div>
            <h3 className="font-bold text-slate-700">Spending Breakdown (Year to Date)</h3>
          </div>
           {pieData.length > 0 ? (
            <SimplePieChart data={pieData} />
           ) : (
             <div className="h-48 flex items-center justify-center text-slate-400 text-sm">No spending recorded yet</div>
           )}
        </Card>
      </div>

      <section>
        <div className="flex items-center justify-between mb-4">
          <h2 className="text-xl font-bold text-slate-800 flex items-center gap-2">
            <ImageIcon className="w-5 h-5 text-violet-500" />
            Dream Board
          </h2>
          <button 
            onClick={() => setIsAddingImage(true)}
            className="text-sm font-medium text-indigo-600 hover:text-indigo-700 flex items-center gap-1"
          >
            <Plus className="w-4 h-4" /> Add Image
          </button>
        </div>

        {isAddingImage && (
          <div className="mb-6 bg-white p-4 rounded-xl shadow-sm border border-indigo-100 flex gap-2 animate-in slide-in-from-top-2">
            <input 
              type="text" 
              placeholder="Paste image URL here (https://...)"
              value={newImageLink}
              onChange={(e) => setNewImageLink(e.target.value)}
              className="flex-1 bg-slate-50 border-none rounded-lg px-4 py-2 text-sm focus:ring-2 focus:ring-indigo-500 outline-none"
            />
            <button onClick={addImage} className="bg-indigo-600 text-white px-4 py-2 rounded-lg text-sm font-medium hover:bg-indigo-700">
              Add
            </button>
            <button onClick={() => setIsAddingImage(false)} className="p-2 text-slate-400 hover:text-slate-600">
              <X className="w-5 h-5" />
            </button>
          </div>
        )}

        <div className="grid grid-cols-2 md:grid-cols-4 gap-4">
          {dreamBoard.map((img) => (
            <div key={img.id} className="group relative aspect-square bg-white rounded-xl overflow-hidden shadow-sm border border-slate-100 hover:shadow-md transition-all">
              <img src={img.url} alt="Dream" className="w-full h-full object-cover" onError={(e) => e.target.src = 'https://via.placeholder.com/300?text=Invalid+URL'} />
              <div className="absolute inset-0 bg-black/0 group-hover:bg-black/20 transition-colors flex items-start justify-end p-2">
                <button 
                  onClick={() => removeImage(img.id)}
                  className="bg-white/90 p-1.5 rounded-full text-rose-500 opacity-0 group-hover:opacity-100 transition-opacity hover:bg-white"
                >
                  <Trash2 className="w-4 h-4" />
                </button>
              </div>
            </div>
          ))}
          {dreamBoard.length === 0 && !isAddingImage && (
             <div className="col-span-2 md:col-span-4 h-32 border-2 border-dashed border-slate-200 rounded-xl flex flex-col items-center justify-center text-slate-400 gap-2">
                <ImageIcon className="w-8 h-8 opacity-50" />
                <span className="text-sm">Add photos of things you are saving for!</span>
             </div>
          )}
        </div>
      </section>
    </div>
  );
};

const BudgetView = ({ user, currentDate, setCurrentDate, financials, setFinancials, handleFinancialChange }) => {
  const [loading, setLoading] = useState(true);
  const [saving, setSaving] = useState(false);
  const [activeTab, setActiveTab] = useState('expenses'); // 'income' | 'expenses' | 'debt'
  
  // Data State
  const [expenseRows, setExpenseRows] = useState([]);
  const [incomeRows, setIncomeRows] = useState([]);
  const [debtRows, setDebtRows] = useState([]);

  const monthKey = useMemo(() => currentDate.toISOString().slice(0, 7), [currentDate]);
  const monthDisplay = useMemo(() => currentDate.toLocaleDateString('en-US', { month: 'long', year: 'numeric' }), [currentDate]);

  // Load Monthly Data
  useEffect(() => {
    const budgetRef = doc(db, 'artifacts', appId, 'users', user.uid, 'months', monthKey);
    const unsubBudget = onSnapshot(budgetRef, (docSnap) => {
      setLoading(false);
      if (docSnap.exists()) {
        const data = docSnap.data();
        setExpenseRows((data.expenseRows || data.rows || []).map(r => ({ ...r, notes: r.notes || '' })));
        setIncomeRows((data.incomeRows || []).map(r => ({ ...r, notes: r.notes || '' })));
        setDebtRows((data.debtRows || []).map(r => ({ ...r, notes: r.notes || '' })));
      } else {
        // Defaults
        const defExp = [
          { id: '1', category: 'Rent / Mortgage', budget: 1500, actual: 1500, notes: '' },
          { id: '2', category: 'Groceries', budget: 400, actual: 0, notes: '' },
          { id: '3', category: 'Utilities', budget: 150, actual: 0, notes: '' },
        ];
        const defInc = [
          { id: '1', source: 'Main Job', planned: 4000, actual: 4000, notes: '' },
          { id: '2', source: 'Side Hustle', planned: 500, actual: 0, notes: '' },
        ];
        const defDebt = [
          { id: '1', name: 'Credit Card', balance: 1500, notes: '' },
          { id: '2', name: 'Car Loan', balance: 12000, notes: '' },
        ];
        
        setExpenseRows(defExp);
        setIncomeRows(defInc);
        setDebtRows(defDebt);
        
        setDoc(budgetRef, { 
            expenseRows: defExp, 
            incomeRows: defInc,
            debtRows: defDebt
        });
      }
    });
    return () => unsubBudget();
  }, [user, monthKey]);

  // Save logic
  const saveData = async (newExp, newInc, newDebt) => {
    setSaving(true);
    try {
      await setDoc(doc(db, 'artifacts', appId, 'users', user.uid, 'months', monthKey), {
        expenseRows: newExp,
        incomeRows: newInc,
        debtRows: newDebt
      }, { merge: true });
    } catch (e) {
      console.error(e);
    } finally {
      setTimeout(() => setSaving(false), 500);
    }
  };

  // Generic Row Handler
  const handleRowChange = (rows, setRows, id, field, value) => {
    const newRows = rows.map(row => row.id === id ? { ...row, [field]: value } : row);
    setRows(newRows);
    // We need to pass current state for other rows to save everything correctly
    saveData(
        rows === expenseRows ? newRows : expenseRows,
        rows === incomeRows ? newRows : incomeRows,
        rows === debtRows ? newRows : debtRows
    );
  };

  const addRow = (rows, setRows, template) => {
    const newRows = [...rows, { ...template, id: Date.now().toString() }];
    setRows(newRows);
    saveData(
        rows === expenseRows ? newRows : expenseRows,
        rows === incomeRows ? newRows : incomeRows,
        rows === debtRows ? newRows : debtRows
    );
  };

  const deleteRow = (rows, setRows, id) => {
    const newRows = rows.filter(r => r.id !== id);
    setRows(newRows);
    saveData(
        rows === expenseRows ? newRows : expenseRows,
        rows === incomeRows ? newRows : incomeRows,
        rows === debtRows ? newRows : debtRows
    );
  };

  const changeMonth = (offset) => {
    const newDate = new Date(currentDate);
    newDate.setMonth(newDate.getMonth() + offset);
    setCurrentDate(newDate);
    setLoading(true);
  };

  // Calculations
  const totalIncome = incomeRows.reduce((acc, r) => acc + (parseFloat(r.actual) || 0), 0);
  const totalExpenses = expenseRows.reduce((acc, r) => acc + (parseFloat(r.actual) || 0), 0);
  const monthlySavings = totalIncome - totalExpenses;
  const totalDebtBalance = debtRows.reduce((acc, r) => acc + (parseFloat(r.balance) || 0), 0);

  return (
    <div className="space-y-8 animate-in slide-in-from-right duration-500">
       {/* Month Nav */}
       <div className="bg-white border border-slate-200 rounded-2xl p-4 flex items-center justify-between shadow-sm sticky top-20 z-10">
          <button onClick={() => changeMonth(-1)} className="p-2 hover:bg-slate-50 rounded-full text-slate-500 hover:text-indigo-600 transition-colors">
            <ChevronLeft className="w-5 h-5" />
          </button>
          <h2 className="text-lg font-bold text-slate-800">{monthDisplay}</h2>
          <button onClick={() => changeMonth(1)} className="p-2 hover:bg-slate-50 rounded-full text-slate-500 hover:text-indigo-600 transition-colors">
            <ChevronRight className="w-5 h-5" />
          </button>
       </div>

       {/* Summary Block */}
       <section className="grid grid-cols-1 md:grid-cols-3 gap-4">
         <div className="bg-white p-4 rounded-xl border border-slate-100 shadow-sm flex flex-col justify-center">
            <p className="text-xs font-bold text-slate-400 uppercase mb-1">Total Income</p>
            <p className="text-2xl font-bold text-slate-700">${totalIncome.toLocaleString()}</p>
         </div>
         <div className="bg-white p-4 rounded-xl border border-slate-100 shadow-sm flex flex-col justify-center">
            <p className="text-xs font-bold text-slate-400 uppercase mb-1">Total Expenses</p>
            <p className="text-2xl font-bold text-slate-700">${totalExpenses.toLocaleString()}</p>
         </div>
         <div className={`p-4 rounded-xl border shadow-sm flex flex-col justify-center relative overflow-hidden ${monthlySavings >= 0 ? 'bg-emerald-50 border-emerald-100' : 'bg-rose-50 border-rose-100'}`}>
            <div className="relative z-10">
                <p className={`text-xs font-bold uppercase mb-1 ${monthlySavings >= 0 ? 'text-emerald-600' : 'text-rose-600'}`}>Monthly Savings</p>
                <p className={`text-2xl font-bold ${monthlySavings >= 0 ? 'text-emerald-700' : 'text-rose-700'}`}>
                    {monthlySavings < 0 ? '-' : ''}${Math.abs(monthlySavings).toLocaleString()}
                </p>
            </div>
            {/* Savings Visualizer */}
            {monthlySavings > 0 && (
                <div className="absolute right-4 top-1/2 -translate-y-1/2 opacity-50 flex items-center gap-2 text-xs font-medium text-emerald-700">
                    <span className="hidden sm:inline">Adds to Total</span>
                    <ArrowRight className="w-4 h-4" />
                </div>
            )}
         </div>
       </section>

       {/* Worksheet Tabs */}
       <div className="flex gap-2 overflow-x-auto pb-2">
         <button 
            onClick={() => setActiveTab('expenses')} 
            className={`px-4 py-2 rounded-lg text-sm font-semibold whitespace-nowrap transition-all flex items-center gap-2 ${activeTab === 'expenses' ? 'bg-indigo-600 text-white shadow-md' : 'bg-white text-slate-500 hover:bg-slate-50'}`}
         >
            <CreditCard className="w-4 h-4" /> Expenses
         </button>
         <button 
            onClick={() => setActiveTab('income')} 
            className={`px-4 py-2 rounded-lg text-sm font-semibold whitespace-nowrap transition-all flex items-center gap-2 ${activeTab === 'income' ? 'bg-indigo-600 text-white shadow-md' : 'bg-white text-slate-500 hover:bg-slate-50'}`}
         >
            <Wallet className="w-4 h-4" /> Income Sources
         </button>
         <button 
            onClick={() => setActiveTab('debt')} 
            className={`px-4 py-2 rounded-lg text-sm font-semibold whitespace-nowrap transition-all flex items-center gap-2 ${activeTab === 'debt' ? 'bg-indigo-600 text-white shadow-md' : 'bg-white text-slate-500 hover:bg-slate-50'}`}
         >
            <Building2 className="w-4 h-4" /> Liabilities / Debt
         </button>
       </div>

       {/* Main Table Area */}
       <section className="bg-white rounded-xl shadow-xl border border-slate-100 overflow-hidden min-h-[400px]">
          <div className="p-4 border-b border-slate-100 bg-slate-50/50 flex justify-between items-center">
            <h3 className="font-bold text-slate-700 capitalize">{activeTab === 'debt' ? 'Debt Balances' : activeTab} Worksheet</h3>
            {saving && <span className="text-xs text-indigo-500 flex items-center gap-1"><Save className="w-3 h-3 animate-pulse" /> Saving...</span>}
          </div>

          <div className="overflow-x-auto">
            <table className="w-full min-w-[600px]">
              <thead>
                <tr className="bg-slate-50 border-b border-slate-100">
                  <th className="text-left py-3 px-4 text-xs font-bold text-slate-400 uppercase w-1/3">
                    {activeTab === 'income' ? 'Source' : activeTab === 'debt' ? 'Creditor' : 'Category'}
                  </th>
                  {activeTab !== 'debt' && (
                     <th className="text-left py-3 px-4 text-xs font-bold text-slate-400 uppercase w-1/6">
                        {activeTab === 'income' ? 'Planned' : 'Budget'}
                     </th>
                  )}
                  <th className="text-left py-3 px-4 text-xs font-bold text-slate-400 uppercase w-1/6">
                    {activeTab === 'debt' ? 'Current Balance' : 'Actual'}
                  </th>
                  {activeTab !== 'debt' && <th className="text-left py-3 px-4 text-xs font-bold text-slate-400 uppercase w-1/6">Diff</th>}
                  <th className="text-left py-3 px-4 text-xs font-bold text-slate-400 uppercase w-1/3">Notes</th>
                  <th className="w-12"></th>
                </tr>
              </thead>
              <tbody className="divide-y divide-slate-50">
                {loading ? (
                  <tr><td colSpan="6" className="py-12 text-center text-slate-400">Loading...</td></tr>
                ) : (
                    activeTab === 'expenses' ? expenseRows : 
                    activeTab === 'income' ? incomeRows : 
                    debtRows
                ).map((row) => {
                  // Diff logic varies by tab
                  let diff = 0;
                  if (activeTab === 'expenses') diff = (parseFloat(row.budget)||0) - (parseFloat(row.actual)||0);
                  if (activeTab === 'income') diff = (parseFloat(row.actual)||0) - (parseFloat(row.planned)||0);

                  return (
                    <tr key={row.id} className="group hover:bg-indigo-50/30 transition-colors">
                      {/* Name / Category Column */}
                      <td className="py-2 px-4 align-top">
                        <input 
                          type="text" 
                          value={activeTab === 'income' ? row.source : activeTab === 'debt' ? row.name : row.category} 
                          onChange={(e) => handleRowChange(
                              activeTab === 'expenses' ? expenseRows : activeTab === 'income' ? incomeRows : debtRows,
                              activeTab === 'expenses' ? setExpenseRows : activeTab === 'income' ? setIncomeRows : setDebtRows,
                              row.id, 
                              activeTab === 'income' ? 'source' : activeTab === 'debt' ? 'name' : 'category', 
                              e.target.value
                          )} 
                          className="w-full bg-transparent font-medium text-slate-700 focus:text-indigo-600 py-2 px-2 rounded hover:bg-white focus:bg-white outline-none transition-all" 
                          placeholder="Name..."
                        />
                      </td>

                      {/* Planned / Budget Column (Hidden for Debt) */}
                      {activeTab !== 'debt' && (
                        <td className="py-2 px-4 align-top">
                            <div className="relative">
                            <span className="absolute left-2 top-1/2 -translate-y-1/2 text-slate-300 text-xs">$</span>
                            <input 
                                type="number" 
                                value={activeTab === 'income' ? row.planned : row.budget} 
                                onChange={(e) => handleRowChange(
                                    activeTab === 'expenses' ? expenseRows : incomeRows,
                                    activeTab === 'expenses' ? setExpenseRows : setIncomeRows,
                                    row.id, 
                                    activeTab === 'income' ? 'planned' : 'budget', 
                                    e.target.value
                                )} 
                                className="w-full bg-slate-50/50 font-medium text-slate-600 outline-none rounded pl-5 pr-2 py-2 hover:border-slate-200 border border-transparent focus:bg-white focus:border-indigo-200 transition-all" 
                            />
                            </div>
                        </td>
                      )}

                      {/* Actual / Balance Column */}
                      <td className="py-2 px-4 align-top">
                         <div className="relative">
                           <span className="absolute left-2 top-1/2 -translate-y-1/2 text-slate-300 text-xs">$</span>
                           <input 
                                type="number" 
                                value={activeTab === 'debt' ? row.balance : row.actual} 
                                onChange={(e) => handleRowChange(
                                    activeTab === 'expenses' ? expenseRows : activeTab === 'income' ? incomeRows : debtRows,
                                    activeTab === 'expenses' ? setExpenseRows : activeTab === 'income' ? setIncomeRows : setDebtRows,
                                    row.id, 
                                    activeTab === 'debt' ? 'balance' : 'actual', 
                                    e.target.value
                                )} 
                                className="w-full bg-slate-50/50 font-medium text-slate-800 outline-none rounded pl-5 pr-2 py-2 hover:border-slate-200 border border-transparent focus:bg-white focus:border-indigo-200 transition-all" 
                            />
                         </div>
                      </td>

                      {/* Diff Column (Hidden for Debt) */}
                      {activeTab !== 'debt' && (
                        <td className="py-2 px-4 align-top">
                            <div className={`mt-1 flex items-center gap-1 font-semibold text-sm px-2 py-1 rounded w-fit ${diff >= 0 ? 'text-emerald-600 bg-emerald-50' : 'text-rose-600 bg-rose-50'}`}>
                            <span>{diff < 0 ? '-' : ''}${Math.abs(diff).toLocaleString()}</span>
                            </div>
                        </td>
                      )}

                      {/* Notes Column */}
                      <td className="py-2 px-4 align-top relative">
                        <div className="relative group">
                           <textarea 
                                value={row.notes} 
                                onChange={(e) => handleRowChange(
                                    activeTab === 'expenses' ? expenseRows : activeTab === 'income' ? incomeRows : debtRows,
                                    activeTab === 'expenses' ? setExpenseRows : activeTab === 'income' ? setIncomeRows : setDebtRows,
                                    row.id, 
                                    'notes', 
                                    e.target.value
                                )} 
                                className="w-full bg-slate-50/30 text-slate-500 focus:text-slate-700 text-sm py-2 px-2 rounded hover:bg-white focus:bg-white transition-all outline-none resize-none h-[38px] focus:h-[80px] focus:absolute focus:z-20 focus:shadow-md focus:border-indigo-100 border border-transparent" 
                                placeholder="Details..." 
                            />
                        </div>
                      </td>

                      <td className="py-2 px-2 text-center align-top">
                        <button 
                            onClick={() => deleteRow(
                                activeTab === 'expenses' ? expenseRows : activeTab === 'income' ? incomeRows : debtRows,
                                activeTab === 'expenses' ? setExpenseRows : activeTab === 'income' ? setIncomeRows : setDebtRows,
                                row.id
                            )} 
                            className="mt-2 p-1 text-slate-300 hover:text-rose-500 hover:bg-rose-50 rounded opacity-0 group-hover:opacity-100 transition-all"
                        >
                            <Trash2 className="w-4 h-4" />
                        </button>
                      </td>
                    </tr>
                  )
                })}
              </tbody>
            </table>
          </div>
          
          <div className="p-4 border-t border-slate-100 bg-slate-50">
            <button 
                onClick={() => addRow(
                    activeTab === 'expenses' ? expenseRows : activeTab === 'income' ? incomeRows : debtRows,
                    activeTab === 'expenses' ? setExpenseRows : activeTab === 'income' ? setIncomeRows : setDebtRows,
                    activeTab === 'expenses' ? { category: 'New Expense', budget: 0, actual: 0, notes: '' } :
                    activeTab === 'income' ? { source: 'New Source', planned: 0, actual: 0, notes: '' } :
                    { name: 'New Creditor', balance: 0, notes: '' }
                )}
                className="flex items-center gap-2 text-indigo-600 font-semibold px-4 py-2 text-sm rounded-lg hover:bg-indigo-100 w-full sm:w-auto justify-center transition-colors"
            >
                <Plus className="w-4 h-4" /> 
                Add {activeTab === 'income' ? 'Source' : activeTab === 'debt' ? 'Liability' : 'Category'}
            </button>
          </div>
       </section>

       {/* Bottom Cards */}
       <section className="grid grid-cols-1 md:grid-cols-3 gap-6">
          {/* Left Card: Total Savings */}
          <StatCard 
            icon={TrendingUp} 
            label="Total Savings" 
            value={financials.currentSavings + (monthlySavings > 0 ? monthlySavings : 0)} 
            goal={financials.savingsGoal || 100000} 
            goalLabel="Goal" 
            colorClass="bg-indigo-500" 
            type="input" 
            onChange={(val) => handleFinancialChange('currentSavings', val)} 
            onGoalChange={(val) => handleFinancialChange('savingsGoal', val)} 
            subtext={`Includes this month's +$${Math.max(monthlySavings, 0).toLocaleString()}`}
          />

          {/* Middle Card: Total Debt */}
          <StatCard 
            icon={CreditCard} 
            label="Total Debt" 
            value={totalDebtBalance} 
            goal={financials.debtLimit || 10000} 
            goalLabel="Limit" 
            colorClass="bg-rose-500" 
            type="input" 
            onChange={(val) => {}} // Read-only mostly, derived from sheet
            onGoalChange={(val) => handleFinancialChange('debtLimit', val)}
            subtext="Calculated from Liabilities tab"
          />

          {/* Right Card: Monthly Savings Target (replaces Net Worth) */}
          <StatCard 
            icon={PiggyBank} 
            label="Monthly Savings Target" 
            value={monthlySavings} 
            goal={financials.monthlySavingsGoal || 2000} 
            goalLabel="Target" 
            colorClass="bg-emerald-500" 
            type="input"
            readOnlyValue={true}
            onGoalChange={(val) => handleFinancialChange('monthlySavingsGoal', val)} 
            subtext={monthlySavings >= (financials.monthlySavingsGoal || 2000) ? "Target Reached! 🎉" : "Keep saving!"}
          />
       </section>
    </div>
  );
};

// --- Main App Container ---

export default function BudgetApp() {
  const [user, setUser] = useState(null);
  const [currentView, setCurrentView] = useState('budget'); // 'dashboard' | 'budget'
  const [currentDate, setCurrentDate] = useState(new Date());
  const [financials, setFinancials] = useState({
    netWorth: 50000,
    netWorthGoal: 100000,
    totalDebt: 12000,
    debtLimit: 5000,
    currentSavings: 25000,
    savingsGoal: 50000,
    monthlySavingsGoal: 2000 // Added default
  });

  // Auth
  useEffect(() => {
    const initAuth = async () => {
      if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
        await signInWithCustomToken(auth, __initial_auth_token);
      } else {
        await signInAnonymously(auth);
      }
    };
    initAuth();
    return onAuthStateChanged(auth, setUser);
  }, []);

  // Global Financials
  useEffect(() => {
    if (!user) return;
    const financialsRef = doc(db, 'artifacts', appId, 'users', user.uid, 'settings', 'financials');
    return onSnapshot(financialsRef, (docSnap) => {
      if (docSnap.exists()) setFinancials(prev => ({ ...prev, ...docSnap.data() }));
      else setDoc(financialsRef, financials); 
    });
  }, [user]);

  const handleFinancialChange = (key, value) => {
    const newData = { ...financials, [key]: value };
    setFinancials(newData);
    // Simple debounce
    const t = setTimeout(() => {
      if (user) setDoc(doc(db, 'artifacts', appId, 'users', user.uid, 'settings', 'financials'), newData);
    }, 800);
  };

  if (!user) return <div className="min-h-screen flex items-center justify-center bg-slate-50 text-slate-400"><div className="animate-pulse flex flex-col items-center"><div className="h-12 w-12 bg-slate-200 rounded-full mb-4"></div><p>Loading...</p></div></div>;

  return (
    <div className="min-h-screen bg-slate-50 font-sans text-slate-800 pb-20">
      <header className="bg-white border-b border-slate-200 sticky top-0 z-20 shadow-sm">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 h-16 flex items-center justify-between">
          <button 
            onClick={() => setCurrentView('dashboard')}
            className="flex items-center gap-2 text-indigo-600 hover:bg-indigo-50 px-3 py-2 rounded-xl transition-all group"
          >
            <div className="p-2 bg-indigo-100 rounded-lg group-hover:bg-white group-hover:shadow-sm transition-all">
              <DollarSign className="w-6 h-6" />
            </div>
            <span className="font-bold text-xl tracking-tight text-slate-800">WealthFlow</span>
          </button>

          <nav className="flex gap-1 bg-slate-100 p-1 rounded-xl">
            <button 
              onClick={() => setCurrentView('dashboard')} 
              className={`px-4 py-2 rounded-lg text-sm font-semibold transition-all flex items-center gap-2 ${currentView === 'dashboard' ? 'bg-white shadow text-indigo-600' : 'text-slate-500 hover:text-slate-700'}`}
            >
              <LayoutDashboard className="w-4 h-4" /> Dashboard
            </button>
            <button 
              onClick={() => setCurrentView('budget')} 
              className={`px-4 py-2 rounded-lg text-sm font-semibold transition-all flex items-center gap-2 ${currentView === 'budget' ? 'bg-white shadow text-indigo-600' : 'text-slate-500 hover:text-slate-700'}`}
            >
              <FileText className="w-4 h-4" /> Worksheet
            </button>
          </nav>
        </div>
      </header>

      <main className="max-w-7xl mx-auto px-4 sm:px-6 py-8">
        {currentView === 'dashboard' ? (
          <DashboardView 
            user={user} 
            financials={financials}
            onNavigateToMonth={(monthId) => {
               setCurrentDate(new Date(monthId + '-02')); // Add day to avoid timezone shift
               setCurrentView('budget');
            }}
          />
        ) : (
          <BudgetView 
            user={user} 
            currentDate={currentDate} 
            setCurrentDate={setCurrentDate}
            financials={financials}
            setFinancials={setFinancials}
            handleFinancialChange={handleFinancialChange}
          />
        )}
      </main>
    </div>
  );
}