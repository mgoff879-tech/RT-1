import React, { useState, useEffect } from 'react';
import { Plus, Trash2, Save, Undo2, Calculator, History, FileSpreadsheet } from 'lucide-react';

const SCORE_VALUES = {
  '-': 3.0,
  '3': 3.0,
  '+': 3.5,
  '4': 4.0
};

const SCORE_KEYS = ['-', '3', '+', '4'];

const App = () => {
  // State for the list of saved barns
  const [savedBarns, setSavedBarns] = useState(() => {
    const saved = localStorage.getItem('rooster-scores-data');
    return saved ? JSON.parse(saved) : [];
  });

  // State for the current active session
  const [isCreating, setIsCreating] = useState(false);
  const [currentName, setCurrentName] = useState('');
  const [currentCounts, setCurrentCounts] = useState({ '-': 0, '3': 0, '+': 0, '4': 0 });
  const [history, setHistory] = useState([]); // For undo functionality

  // Persist to local storage whenever savedBarns changes
  useEffect(() => {
    localStorage.setItem('rooster-scores-data', JSON.stringify(savedBarns));
  }, [savedBarns]);

  // --- Actions ---

  const handleScoreClick = (key) => {
    // Add to history for undo
    setHistory([...history, key]);
    
    // Update counts
    setCurrentCounts(prev => ({
      ...prev,
      [key]: prev[key] + 1
    }));
  };

  const handleUndo = () => {
    if (history.length === 0) return;
    
    const lastKey = history[history.length - 1];
    const newHistory = history.slice(0, -1);
    
    setHistory(newHistory);
    setCurrentCounts(prev => ({
      ...prev,
      [lastKey]: prev[lastKey] - 1
    }));
  };

  const saveBarn = () => {
    if (!currentName.trim()) {
      alert("Please enter a barn name");
      return;
    }
    
    const newBarn = {
      id: Date.now().toString(),
      name: currentName,
      timestamp: Date.now(),
      counts: currentCounts,
      // Calculate stats at save time to freeze them, or calc on render (doing on render below)
    };

    setSavedBarns([newBarn, ...savedBarns]); // Newest first
    resetForm();
  };

  const deleteBarn = (id) => {
    if (window.confirm("Are you sure you want to delete this barn's data?")) {
      setSavedBarns(savedBarns.filter(b => b.id !== id));
    }
  };

  const resetForm = () => {
    setIsCreating(false);
    setCurrentName('');
    setCurrentCounts({ '-': 0, '3': 0, '+': 0, '4': 0 });
    setHistory([]);
  };

  // --- Calculation Helpers ---

  const calculateStats = (counts) => {
    let totalRoosters = 0;
    let totalScoreSum = 0;
    const sums = {};
    const percents = {};

    // 1. Count totals and individual sums
    SCORE_KEYS.forEach(key => {
      const count = counts[key];
      const value = SCORE_VALUES[key];
      const sum = count * value;
      
      sums[key] = sum;
      totalRoosters += count;
      totalScoreSum += sum;
    });

    // 2. Averages and Percents
    const averageScore = totalRoosters > 0 ? (totalScoreSum / totalRoosters) : 0;
    
    SCORE_KEYS.forEach(key => {
      percents[key] = totalRoosters > 0 ? ((counts[key] / totalRoosters) * 100) : 0;
    });

    return { totalRoosters, totalScoreSum, averageScore, sums, percents };
  };

  // --- Render Components ---

  // The strict table layout requested
  const ResultsTable = ({ counts, name }) => {
    const { totalRoosters, totalScoreSum, averageScore, sums, percents } = calculateStats(counts);

    return (
      <div className="w-full overflow-x-auto bg-white rounded-lg shadow-sm border border-gray-200 mb-6">
        <div className="p-3 bg-gray-50 border-b border-gray-200 font-bold text-gray-700 flex justify-between items-center">
          <span>{name || "Untitled Barn"}</span>
          <span className="text-xs font-normal text-gray-500">Total: {totalRoosters}</span>
        </div>
        <table className="w-full text-sm text-center border-collapse">
          {/* Row 1: Sum per character, total score, and average */}
          <thead>
            <tr className="bg-blue-50 text-blue-900 font-semibold border-b border-blue-100">
              {SCORE_KEYS.map(key => (
                <td key={`sum-${key}`} className="p-2 border-r border-blue-100">{sums[key].toFixed(1)}</td>
              ))}
              <td className="p-2 border-r border-blue-100 bg-blue-100">{totalScoreSum.toFixed(1)}</td>
              <td className="p-2 bg-blue-100">{averageScore.toFixed(2)}</td>
            </tr>
          </thead>
          
          <tbody>
            {/* Row 2: Characters (-, 3, +, 4), then Total, Avg labels */}
            <tr className="bg-gray-100 text-gray-600 font-bold border-b border-gray-200">
              {SCORE_KEYS.map(key => (
                <td key={`head-${key}`} className="p-2 border-r border-gray-200">{key}</td>
              ))}
              <td className="p-2 border-r border-gray-200">Total</td>
              <td className="p-2">Avg</td>
            </tr>

            {/* Row 3: Count per character, total roosters, and a blank cell */}
            <tr className="border-b border-gray-200">
              {SCORE_KEYS.map(key => (
                <td key={`count-${key}`} className="p-2 border-r border-gray-200 font-mono">{counts[key]}</td>
              ))}
              <td className="p-2 border-r border-gray-200 font-bold">{totalRoosters}</td>
              <td className="p-2 bg-gray-50"></td>
            </tr>

            {/* Row 4: Percentage per character, 100% in Total, and a blank cell */}
            <tr className="text-gray-500 text-xs">
              {SCORE_KEYS.map(key => (
                <td key={`pct-${key}`} className="p-2 border-r border-gray-200">{percents[key].toFixed(2)}%</td>
              ))}
              <td className="p-2 border-r border-gray-200 font-bold">100%</td>
              <td className="p-2 bg-gray-50"></td>
            </tr>
          </tbody>
        </table>
      </div>
    );
  };

  return (
    <div className="min-h-screen bg-slate-100 p-4 md:p-6 font-sans text-gray-800">
      <div className="max-w-2xl mx-auto">
        
        {/* Header */}
        <header className="mb-6 flex items-center justify-between">
          <div className="flex items-center gap-2">
            <Calculator className="text-blue-600" size={28} />
            <h1 className="text-2xl font-bold text-slate-800">Flesh Score</h1>
          </div>
          {!isCreating && (
            <button 
              onClick={() => setIsCreating(true)}
              className="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg flex items-center gap-2 shadow-sm transition-colors"
            >
              <Plus size={20} />
              New Barn
            </button>
          )}
        </header>

        {/* Input Mode */}
        {isCreating ? (
          <div className="bg-white p-6 rounded-xl shadow-lg border border-gray-200 animate-in fade-in slide-in-from-bottom-4">
            <div className="mb-6">
              <label className="block text-sm font-medium text-gray-700 mb-1">Barn Name / Identifier</label>
              <input 
                autoFocus
                type="text" 
                placeholder="e.g. Barn 4 - South Side"
                value={currentName}
                onChange={(e) => setCurrentName(e.target.value)}
                className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none transition-all"
              />
            </div>

            {/* Score Buttons Grid */}
            <div className="grid grid-cols-4 gap-3 mb-6">
              {SCORE_KEYS.map((key) => (
                <button
                  key={key}
                  onClick={() => handleScoreClick(key)}
                  className="aspect-square flex flex-col items-center justify-center bg-slate-50 hover:bg-blue-50 border-2 border-slate-200 hover:border-blue-500 rounded-xl transition-all active:scale-95 group"
                >
                  <span className="text-3xl font-bold text-slate-700 group-hover:text-blue-600 mb-1">{key}</span>
                  <span className="text-xs text-slate-400 font-medium">Value: {SCORE_VALUES[key]}</span>
                </button>
              ))}
            </div>

            {/* Live Preview & Controls */}
            <div className="mb-6">
              <div className="flex justify-between items-center mb-2">
                <span className="text-sm font-semibold text-gray-500">Live Preview</span>
                {history.length > 0 && (
                  <button 
                    onClick={handleUndo}
                    className="text-sm text-red-500 flex items-center gap-1 hover:text-red-700 px-2 py-1 rounded bg-red-50"
                  >
                    <Undo2 size={16} /> Undo Last
                  </button>
                )}
              </div>
              <ResultsTable counts={currentCounts} name={currentName || "Current Session"} />
            </div>

            <div className="flex gap-3">
              <button 
                onClick={saveBarn}
                className="flex-1 bg-green-600 hover:bg-green-700 text-white py-3 rounded-lg font-semibold flex justify-center items-center gap-2 shadow-sm transition-colors"
              >
                <Save size={20} /> Save Barn
              </button>
              <button 
                onClick={resetForm}
                className="px-6 py-3 border border-gray-300 text-gray-700 rounded-lg font-medium hover:bg-gray-50 transition-colors"
              >
                Cancel
              </button>
            </div>
          </div>
        ) : (
          /* List Mode */
          <div className="space-y-6">
            {savedBarns.length === 0 ? (
              <div className="text-center py-16 bg-white rounded-xl border-2 border-dashed border-gray-300">
                <FileSpreadsheet className="mx-auto text-gray-300 mb-3" size={48} />
                <p className="text-gray-500 font-medium">No barns saved yet.</p>
                <p className="text-gray-400 text-sm">Click "New Barn" to start scoring.</p>
              </div>
            ) : (
              savedBarns.map((barn) => (
                <div key={barn.id} className="relative group">
                  <ResultsTable counts={barn.counts} name={barn.name} />
                  <div className="absolute top-3 right-3 opacity-0 group-hover:opacity-100 transition-opacity">
                    <button 
                      onClick={() => deleteBarn(barn.id)}
                      className="p-2 bg-white text-red-500 rounded-full shadow border border-gray-200 hover:bg-red-50"
                      title="Delete Record"
                    >
                      <Trash2 size={16} />
                    </button>
                  </div>
                  <div className="absolute bottom-2 right-2 text-[10px] text-gray-300">
                    {new Date(barn.timestamp).toLocaleDateString()}
                  </div>
                </div>
              ))
            )}
            
            {savedBarns.length > 0 && (
              <div className="flex items-center gap-2 text-gray-400 text-sm justify-center mt-8">
                <History size={16} />
                <span>History stored on this device</span>
              </div>
            )}
          </div>
        )}

      </div>
    </div>
  );
};

export default App;

