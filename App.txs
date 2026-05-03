import React, { useState, useEffect, useRef, useMemo, MouseEvent, ReactNode } from 'react';
import { 
  Plus, 
  Trash2, 
  Save, 
  Printer, 
  LogOut, 
  Key, 
  FileText, 
  ChevronRight, 
  Menu, 
  X, 
  Download, 
  Layout, 
  CheckCircle2, 
  Clock, 
  ShieldCheck, 
  Maximize2 
} from 'lucide-react';
import { motion, AnimatePresence } from 'motion/react';

// --- Types ---

interface QuoteItem {
  id: string;
  name: string;
  description: string;
  width: string;
  height: string;
  unit: string;
  rate: string;
}

interface Room {
  id: string;
  name: string;
  items: QuoteItem[];
}

interface QuoteData {
  id: string;
  customerName: string;
  projectName: string;
  createdBy: string;
  quoteNumber: string;
  date: string;
  isGstEnabled: boolean;
  rooms: Room[];
  lastSaved: number;
}

interface AuthUser {
  username: string;
  pass: string;
}

// --- Constants ---

const INITIAL_ROOMS = [
  "Master Bedroom",
  "Kids Bedroom",
  "GBR Bedroom",
  "Kitchen",
  "Hall",
  "Balcony",
  "Other Works"
];

const YELLOW_ACCENT = "#ffd400";
const BORDER_COLOR = "#222";

// --- Calculations ---

function calculateItemTotal(item: QuoteItem) {
  const w = parseFloat(item.width) || 0;
  const h = parseFloat(item.height) || 0;
  const r = parseFloat(item.rate) || 0;
  if (h === 0) return w * r;
  return w * h * r;
}

function calculateRoomSubtotal(room: Room) {
  return room.items.reduce((sum, item) => sum + calculateItemTotal(item), 0);
}

export default function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const [user, setUser] = useState<AuthUser | null>(null);
  const [quoteData, setQuoteData] = useState<QuoteData | null>(null);
  const [savedQuotes, setSavedQuotes] = useState<QuoteData[]>([]);
  const [activeRoomId, setActiveRoomId] = useState<string>("");
  const [isSidebarOpen, setIsSidebarOpen] = useState(true);
  const [isSavedQuotesModalOpen, setIsSavedQuotesModalOpen] = useState(false);
  const [isChangePassModalOpen, setIsChangePassModalOpen] = useState(false);

  // --- Auth & Data Loading ---

  useEffect(() => {
    try {
      const storedUser = localStorage.getItem('dwaram_user');
      if (storedUser) {
        const parsedUser = JSON.parse(storedUser);
        setUser(parsedUser);
        // Auto-login if session exists
        const session = localStorage.getItem('dwaram_session');
        if (session === 'true') {
          setIsLoggedIn(true);
          loadUserQuote(parsedUser.username);
        }
      } else {
        // Default credentials
        const defaultUser = { username: 'dwaram', pass: '1234' };
        setUser(defaultUser);
        localStorage.setItem('dwaram_user', JSON.stringify(defaultUser));
      }

      const storedSavedQuotes = localStorage.getItem('dwaram_saved_quotes');
      if (storedSavedQuotes) {
        setSavedQuotes(JSON.parse(storedSavedQuotes));
      }
    } catch (e) {
      console.error("Error loading session:", e);
      // Reset if corrupted
      const defaultUser = { username: 'dwaram', pass: '1234' };
      setUser(defaultUser);
      localStorage.setItem('dwaram_user', JSON.stringify(defaultUser));
    }
  }, []);

  const loadUserQuote = (username: string) => {
    try {
      const storedQuote = localStorage.getItem(`dwaram_quote_${username}`);
      if (storedQuote) {
        const parsed = JSON.parse(storedQuote);
        setQuoteData(parsed);
        if (parsed.rooms.length > 0) {
          setActiveRoomId(parsed.rooms[0].id);
        }
      } else {
        createNewQuote();
      }
    } catch (e) {
      console.error("Error loading quote:", e);
      createNewQuote();
    }
  };

  const createNewQuote = () => {
    const newQuote: QuoteData = {
      id: Math.random().toString(36).substr(2, 9),
      customerName: "",
      projectName: "",
      createdBy: "Dwaram Interiors",
      quoteNumber: `DI-${new Date().getFullYear()}-${Math.floor(1000 + Math.random() * 9000)}`,
      date: new Date().toISOString().split('T')[0],
      isGstEnabled: false,
      rooms: INITIAL_ROOMS.map(name => ({
        id: Math.random().toString(36).substr(2, 9),
        name,
        items: []
      })),
      lastSaved: Date.now()
    };
    setQuoteData(newQuote);
    if (newQuote.rooms.length > 0) {
      setActiveRoomId(newQuote.rooms[0].id);
    }
  };

  const handleLogin = (u: string, p: string) => {
    if (user && u === user.username && p === user.pass) {
      setIsLoggedIn(true);
      localStorage.setItem('dwaram_session', 'true');
      loadUserQuote(u);
    } else {
      alert("Invalid credentials");
    }
  };

  const handleLogout = () => {
    setIsLoggedIn(false);
    localStorage.removeItem('dwaram_session');
  };

  // --- Auto-save ---

  useEffect(() => {
    if (isLoggedIn && quoteData && user) {
      localStorage.setItem(`dwaram_quote_${user.username}`, JSON.stringify({
        ...quoteData,
        lastSaved: Date.now()
      }));
    }
  }, [quoteData, isLoggedIn, user]);

  // --- Calculations ---

  const totalBeforeGst = useMemo(() => {
    if (!quoteData) return 0;
    return quoteData.rooms.reduce((sum, room) => sum + calculateRoomSubtotal(room), 0);
  }, [quoteData]);

  const gstValue = useMemo(() => {
    if (!quoteData?.isGstEnabled) return 0;
    return totalBeforeGst * 0.18;
  }, [totalBeforeGst, quoteData?.isGstEnabled]);

  const grandTotal = totalBeforeGst + gstValue;

  // --- Actions ---

  const addRoom = () => {
    const name = prompt("Enter room name:");
    if (name && quoteData) {
      const newRoom: Room = {
        id: Math.random().toString(36).substr(2, 9),
        name,
        items: []
      };
      setQuoteData({
        ...quoteData,
        rooms: [...quoteData.rooms, newRoom]
      });
      setActiveRoomId(newRoom.id);
    }
  };

  const deleteRoom = (id: string, e: MouseEvent) => {
    e.stopPropagation();
    if (!quoteData) return;
    if (confirm("Are you sure you want to delete this room?")) {
      const newRooms = quoteData.rooms.filter(r => r.id !== id);
      setQuoteData({ ...quoteData, rooms: newRooms });
      if (activeRoomId === id && newRooms.length > 0) {
        setActiveRoomId(newRooms[0].id);
      }
    }
  };

  const addItem = () => {
    if (!quoteData || !activeRoomId) return;
    const newItems = [...(quoteData.rooms.find(r => r.id === activeRoomId)?.items || [])];
    newItems.push({
      id: Math.random().toString(36).substr(2, 9),
      name: "",
      description: "",
      width: "",
      height: "",
      unit: "SFT",
      rate: ""
    });
    const newRooms = quoteData.rooms.map(r => 
      r.id === activeRoomId ? { ...r, items: newItems } : r
    );
    setQuoteData({ ...quoteData, rooms: newRooms });
  };

  const deleteItem = (itemId: string) => {
    if (!quoteData || !activeRoomId) return;
    const newRooms = quoteData.rooms.map(r => {
      if (r.id === activeRoomId) {
        return { ...r, items: r.items.filter(i => i.id !== itemId) };
      }
      return r;
    });
    setQuoteData({ ...quoteData, rooms: newRooms });
  };

  const updateItem = (itemId: string, field: keyof QuoteItem, value: string) => {
    if (!quoteData || !activeRoomId) return;
    const newRooms = quoteData.rooms.map(r => {
      if (r.id === activeRoomId) {
        return {
          ...r,
          items: r.items.map(i => i.id === itemId ? { ...i, [field]: value } : i)
        };
      }
      return r;
    });
    setQuoteData({ ...quoteData, rooms: newRooms });
  };

  const handleSaveToQuoteList = () => {
    if (!quoteData) return;
    const existingIndex = savedQuotes.findIndex(q => q.id === quoteData.id);
    let newList;
    if (existingIndex > -1) {
      newList = [...savedQuotes];
      newList[existingIndex] = { ...quoteData, lastSaved: Date.now() };
    } else {
      newList = [...savedQuotes, { ...quoteData, lastSaved: Date.now() }];
    }
    setSavedQuotes(newList);
    localStorage.setItem('dwaram_saved_quotes', JSON.stringify(newList));
    alert("Quote saved successfully!");
  };

  const loadSavedQuote = (quote: QuoteData) => {
    setQuoteData(quote);
    if (quote.rooms.length > 0) {
      setActiveRoomId(quote.rooms[0].id);
    }
    setIsSavedQuotesModalOpen(false);
  };

  const deleteSavedQuote = (id: string) => {
    if (confirm("Delete this saved quote?")) {
      const newList = savedQuotes.filter(q => q.id !== id);
      setSavedQuotes(newList);
      localStorage.setItem('dwaram_saved_quotes', JSON.stringify(newList));
    }
  };

  const handlePrint = () => {
    // Ensure all data is rendered and window is focused
    window.focus();
    setTimeout(() => {
      window.print();
    }, 100);
  };

  const handleDownloadHtml = () => {
    if (!quoteData) return;
    const printArea = document.getElementById('print-area');
    if (!printArea) return;
    
    const printContent = printArea.innerHTML;
    
    // Extract all current CSS rules from the document
    let allCss = '';
    try {
      for (let i = 0; i < document.styleSheets.length; i++) {
        const sheet = document.styleSheets[i];
        try {
          const rules = sheet.cssRules || sheet.rules;
          for (let j = 0; j < rules.length; j++) {
            allCss += rules[j].cssText + '\n';
          }
        } catch (e) {
          // Skip cross-origin sheets that we can't access
          console.warn('Could not read stylesheet:', sheet.href);
        }
      }
    } catch (e) {
      console.error('Error extracting styles:', e);
    }

    // Create a standalone HTML file that is fully self-contained
    const htmlString = `
      <!DOCTYPE html>
      <html lang="en">
      <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Dwaram Interiors Quote - ${quoteData.customerName || 'Customer'}</title>
        <style>
          /* Embedded Application Styles */
          ${allCss}
          
          /* Standalone Environment Adjustments */
          body { 
            background-color: #f3f4f6 !important; 
            margin: 0; 
            padding: 0;
            -webkit-print-color-adjust: exact !important; 
            print-color-adjust: exact !important; 
          }
          
          .standalone-wrapper {
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px 0;
          }

          @media screen {
            .page {
              background: white !important;
              box-shadow: 0 10px 25px rgba(0,0,0,0.1) !important;
              margin-bottom: 40px !important;
              border: 1px solid #ddd !important;
            }
          }

          @media print {
            body { background-color: white !important; }
            .page { 
              margin: 0 !important; 
              box-shadow: none !important; 
              border: none !important; 
              width: 100% !important;
              height: 100vh !important;
              page-break-after: always !important;
            }
            .no-print { display: none !important; }
          }
        </style>
      </head>
      <body>
        <div class="standalone-wrapper">
          ${printContent}
        </div>
        <script>
          // Optional: Auto-trigger print on open
          window.onload = () => {
            console.log('Dwaram Interiors Quotation Loaded');
          };
        </script>
      </body>
      </html>
    `;
    
    const blob = new Blob([htmlString], { type: 'text/html' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = `Dwaram_Quote_${(quoteData.customerName || 'Unnamed').replace(/\s+/g, '_')}_${quoteData.quoteNumber}.html`;
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
    URL.revokeObjectURL(url);
  };

  // --- Views ---

  if (!isLoggedIn) {
    return <LoginView onLogin={handleLogin} />;
  }

  if (!quoteData) return null;

  const activeRoom = quoteData.rooms.find(r => r.id === activeRoomId);

  return (
    <div className="min-h-screen bg-gray-50 flex flex-col font-sans text-[#222]">
      {/* Header */}
      <header className="h-[100px] bg-[#ffd400] px-6 py-4 flex flex-col md:flex-row gap-4 items-start md:items-center sticky top-0 z-30 border-b-2 border-[#222] print:hidden">
        <div className="flex items-center gap-3 mr-8 cursor-pointer shrink-0" onClick={createNewQuote}>
          <Logo color="#222" size={44} />
          <div className="hidden lg:block">
            <h1 className="font-black text-xl tracking-tighter uppercase leading-none">Dwaram Interiors</h1>
            <p className="text-[9px] font-bold uppercase tracking-widest mt-1 opacity-70">Quotation System</p>
          </div>
        </div>
        
        <div className="flex-1 grid grid-cols-2 lg:grid-cols-5 gap-3 w-full">
          <HeaderInput 
            label="Customer Name" 
            value={quoteData.customerName} 
            onChange={v => setQuoteData({...quoteData, customerName: v})} 
          />
          <HeaderInput 
            label="Project Name" 
            value={quoteData.projectName} 
            onChange={v => setQuoteData({...quoteData, projectName: v})} 
          />
          <HeaderInput 
            label="Created By" 
            value={quoteData.createdBy} 
            onChange={v => setQuoteData({...quoteData, createdBy: v})} 
          />
          <HeaderInput 
            label="Quote No" 
            value={quoteData.quoteNumber} 
            onChange={v => setQuoteData({...quoteData, quoteNumber: v})} 
          />
          <HeaderInput 
            label="Date" 
            type="date"
            value={quoteData.date} 
            onChange={v => setQuoteData({...quoteData, date: v})} 
          />
        </div>

        <div className="flex gap-2 ml-auto shrink-0">
          <button onClick={() => setIsChangePassModalOpen(true)} className="p-2 border border-[#222] bg-white hover:bg-gray-50 shadow-[2px_2px_0px_#222] active:translate-y-[1px] active:translate-x-[1px] active:shadow-none" title="Change Password">
            <Key size={18} />
          </button>
          <button onClick={handleLogout} className="p-2 border border-[#222] bg-white hover:bg-gray-50 shadow-[2px_2px_0px_#222] active:translate-y-[1px] active:translate-x-[1px] active:shadow-none" title="Logout">
            <LogOut size={18} />
          </button>
        </div>
      </header>

      <div className="flex flex-1 overflow-hidden print:hidden">
        {/* Sidebar */}
        <aside className={`${isSidebarOpen ? 'w-72' : 'w-16'} transition-all duration-300 bg-white border-r border-[#222] flex flex-col z-20`}>
          <div className="p-4 flex items-center justify-between border-b border-[#222]">
            <span className={`font-bold overflow-hidden transition-all ${isSidebarOpen ? 'w-auto' : 'w-0'}`}>ROOMS</span>
            <button onClick={() => setIsSidebarOpen(!isSidebarOpen)} className="p-1 hover:bg-gray-100 rounded">
              {isSidebarOpen ? <X size={20} /> : <Menu size={20} />}
            </button>
          </div>
          
          <div className="flex-1 overflow-y-auto p-2 space-y-2">
            {quoteData.rooms.map(room => (
              <div
                key={room.id}
                onClick={() => setActiveRoomId(room.id)}
                className={`w-full text-left p-3 flex items-center justify-between group transition-all sidebar-item cursor-pointer ${
                  activeRoomId === room.id ? 'room-btn-active' : 'hover:border-[#222]'
                }`}
              >
                <div className="flex items-center gap-3 overflow-hidden">
                  <Layout size={16} className="shrink-0" />
                  <span className="truncate text-sm uppercase font-bold tracking-tight">{room.name}</span>
                </div>
                {isSidebarOpen && room.items.length > 0 && (
                  <span className="text-[9px] bg-black text-white px-1.5 py-0.5 rounded-none font-bold">
                    {room.items.length}
                  </span>
                )}
                <button 
                  onClick={(e) => deleteRoom(room.id, e)}
                  className="opacity-0 group-hover:opacity-100 p-1 hover:bg-gray-100 rounded-none transition-opacity"
                >
                  <Trash2 size={12} />
                </button>
              </div>
            ))}
            
            {isSidebarOpen && (
              <button 
                onClick={addRoom}
                className="w-full flex items-center gap-2 p-3 text-gray-500 hover:text-[#222] hover:bg-white hover:border-[#222] border-2 border-dashed border-gray-300 mt-4 text-xs font-bold uppercase transition-all"
              >
                <Plus size={16} />
                <span>Add Room</span>
              </button>
            )}
          </div>

          {isSidebarOpen && (
            <div className="p-4 bg-gray-50 border-t border-[#222]">
              <div className="text-xs text-gray-500 uppercase font-bold mb-1">Total Valuation</div>
              <div className="text-lg font-black font-mono">₹{grandTotal.toLocaleString()}</div>
            </div>
          )}
        </aside>

        {/* Main Content Area */}
        <main className="flex-1 overflow-y-auto p-6">
          <AnimatePresence mode="wait">
            {activeRoom ? (
              <motion.div
                key={activeRoom.id}
                initial={{ opacity: 0, y: 10 }}
                animate={{ opacity: 1, y: 0 }}
                exit={{ opacity: 0, y: -10 }}
                className="space-y-6"
              >
                <div className="flex items-center justify-between">
                  <div>
                    <h2 className="text-xl font-black uppercase tracking-tighter flex items-center gap-3">
                      {activeRoom.name}
                    </h2>
                    <p className="text-[10px] uppercase font-bold text-gray-400 tracking-widest mt-1">Editing room specific line items</p>
                  </div>
                  <button 
                    onClick={addItem}
                    className="flex items-center gap-2 bg-[#ffd400] text-[#222] border-2 border-[#222] px-4 py-2 font-black uppercase text-xs hover:shadow-[3px_3px_0px_#222] transition-all active:translate-y-[1px] active:translate-x-[1px] active:shadow-none"
                  >
                    <Plus size={16} />
                    ADD NEW ITEM
                  </button>
                </div>

                <div className="overflow-x-auto bg-white border-2 border-[#222]">
                  <table className="w-full border-collapse">
                    <thead>
                      <tr className="bg-[#f3f4f6] border-b-2 border-[#222]">
                        <th className="p-3 text-left w-12 border-r border-[#222] text-[10px] font-black uppercase tracking-wider">S.NO</th>
                        <th className="p-3 text-left flex-1 border-r border-[#222] text-[10px] font-black uppercase tracking-wider">ITEM DESCRIPTION</th>
                        <th className="p-3 text-left w-20 border-r border-[#222] text-[10px] font-black uppercase tracking-wider">W (FT)</th>
                        <th className="p-3 text-left w-20 border-r border-[#222] text-[10px] font-black uppercase tracking-wider">H (FT)</th>
                        <th className="p-3 text-left w-20 border-r border-[#222] text-[10px] font-black uppercase tracking-wider">UNIT</th>
                        <th className="p-3 text-left w-28 border-r border-[#222] text-[10px] font-black uppercase tracking-wider">RATE</th>
                        <th className="p-3 text-left w-32 border-r border-[#222] text-[10px] font-black uppercase tracking-wider">TOTAL</th>
                        <th className="p-3 text-left w-10 text-[10px] font-black uppercase tracking-wider"></th>
                      </tr>
                    </thead>
                    <tbody className="text-sm">
                      {activeRoom.items.length === 0 ? (
                        <tr>
                          <td colSpan={8} className="p-12 text-center text-gray-400 uppercase text-xs font-bold tracking-widest italic bg-gray-50">
                            No items added yet. Click 'ADD NEW ITEM' to start.
                          </td>
                        </tr>
                      ) : (
                        activeRoom.items.map((item, index) => (
                          <tr key={item.id} className="border-b border-[#222] hover:bg-gray-50/50">
                            <td className="p-3 border-r border-[#222] font-mono text-xs text-center">{index + 1}</td>
                            <td className="p-3 border-r border-[#222] min-w-[300px]">
                              <input 
                                className="w-full font-black uppercase text-xs bg-transparent border-none! p-0! outline-none focus:ring-0 mb-1"
                                placeholder="Item Title..."
                                value={item.name}
                                onChange={e => updateItem(item.id, 'name', e.target.value)}
                              />
                              <textarea 
                                className="w-full text-[10px] text-gray-500 bg-transparent border-none! p-0! outline-none focus:ring-0 resize-none h-12 leading-relaxed"
                                placeholder="Add details... (Material, Finish, Hardware etc)"
                                value={item.description}
                                onChange={e => updateItem(item.id, 'description', e.target.value)}
                              />
                            </td>
                            <td className="p-2 border-r border-[#222]">
                              <input 
                                className="w-full font-mono bg-transparent! p-1! border-none! text-right outline-none"
                                type="number"
                                value={item.width}
                                onChange={e => updateItem(item.id, 'width', e.target.value)}
                              />
                            </td>
                            <td className="p-2 border-r border-[#222]">
                              <input 
                                className="w-full font-mono bg-transparent! p-1! border-none! text-right outline-none"
                                type="number"
                                value={item.height}
                                onChange={e => updateItem(item.id, 'height', e.target.value)}
                              />
                            </td>
                            <td className="p-2 border-r border-[#222]">
                              <input 
                                className="w-full font-mono bg-transparent! p-1! border-none! text-center outline-none uppercase text-[10px] font-bold"
                                value={item.unit}
                                onChange={e => updateItem(item.id, 'unit', e.target.value)}
                              />
                            </td>
                            <td className="p-2 border-r border-[#222]">
                              <input 
                                className="w-full font-mono bg-transparent! p-1! border-none! text-right outline-none"
                                type="number"
                                value={item.rate}
                                onChange={e => updateItem(item.id, 'rate', e.target.value)}
                              />
                            </td>
                            <td className="p-3 border-r border-[#222] font-black font-mono text-right bg-gray-50/80">
                              ₹{calculateItemTotal(item).toLocaleString()}
                            </td>
                            <td className="p-3 text-center">
                              <button onClick={() => deleteItem(item.id)} className="text-gray-300 hover:text-[#222] transition-colors">
                                <X size={14} />
                              </button>
                            </td>
                          </tr>
                        ))
                      )}
                    </tbody>
                    <tfoot>
                      <tr className="bg-white font-black border-t-2 border-[#222]">
                        <td colSpan={6} className="p-4 text-right uppercase tracking-widest text-[10px]">Room Amount Estimative</td>
                        <td colSpan={2} className="p-4 font-mono text-xl text-right bg-[#ffd400]">
                          ₹{calculateRoomSubtotal(activeRoom).toLocaleString()}
                        </td>
                      </tr>
                    </tfoot>
                  </table>
                </div>
              </motion.div>
            ) : (
              <div className="h-full flex flex-col items-center justify-center text-gray-400">
                <Layout size={64} className="mb-4 opacity-10" />
                <p>Select a room from the sidebar to start editing</p>
              </div>
            )}
          </AnimatePresence>
        </main>

        {/* Right Summary Panel */}
        <aside className="w-80 bg-gray-50 border-l-2 border-[#222] flex flex-col shadow-xl z-20 shrink-0">
          <div className="bg-[#222] p-4 text-[#ffd400] font-black flex items-center justify-center gap-3 text-sm tracking-widest uppercase">
            <Logo color="#ffd400" size={24} />
            Quotation Summary
          </div>
          
          <div className="flex-1 p-6 space-y-6">
            <div className="bg-white border border-[#222] p-4 space-y-4">
              <div className="flex items-center justify-between">
                <span className="text-xs font-black uppercase tracking-tight text-gray-500">Apply GST (18%)</span>
                <input 
                  type="checkbox" 
                  className="w-5 h-5 accent-[#222]"
                  checked={quoteData.isGstEnabled}
                  onChange={() => setQuoteData({...quoteData, isGstEnabled: !quoteData.isGstEnabled})}
                />
              </div>
              <div className="border-t border-[#222] pt-4 space-y-2">
                <div className="flex justify-between text-xs font-bold uppercase">
                  <span className="text-gray-400">Subtotal:</span>
                  <span className="font-mono text-[#222]">₹{totalBeforeGst.toLocaleString()}</span>
                </div>
                {quoteData.isGstEnabled && (
                  <div className="flex justify-between text-xs font-bold uppercase">
                    <span className="text-gray-400">GST (18%):</span>
                    <span className="font-mono text-[#222]">₹{gstValue.toLocaleString()}</span>
                  </div>
                )}
              </div>
            </div>

            <div className="p-4 bg-[#ffd400] border-2 border-[#222] shadow-[4px_4px_0px_#222] transition-transform">
              <div className="text-[10px] uppercase font-black tracking-widest mb-1">Grand Total Value</div>
              <div className="text-2xl font-black font-mono leading-none">
                ₹{grandTotal.toLocaleString()}
              </div>
            </div>

            <div className="grid grid-cols-1 gap-2 pt-8">
              <button 
                onClick={handleSaveToQuoteList}
                className="w-full py-3 bg-[#222] text-white font-black text-xs uppercase tracking-widest hover:bg-[#444] transition-all"
              >
                SAVE QUOTE
              </button>
              <div className="grid grid-cols-2 gap-2">
                <button 
                  onClick={() => setIsSavedQuotesModalOpen(true)}
                  className="w-full py-3 border-2 border-[#222] bg-white font-black text-xs uppercase tracking-widest hover:bg-gray-50 transition-all flex items-center justify-center gap-2"
                >
                  <Clock size={14} />
                  SAVED
                </button>
                <button 
                  onClick={handleDownloadHtml}
                  className="w-full py-3 border-2 border-[#222] bg-white font-black text-xs uppercase tracking-widest hover:bg-gray-50 transition-all flex items-center justify-center gap-2"
                >
                  <Download size={14} />
                  HTML
                </button>
              </div>
              <button 
                onClick={handlePrint}
                className="w-full py-4 bg-[#ffd400] border-2 border-[#222] font-black text-sm uppercase tracking-tighter hover:shadow-[3px_3px_0px_#222] transition-all active:translate-y-[1px] active:translate-x-[1px] active:shadow-none flex items-center justify-center gap-3"
              >
                <Printer size={20} />
                GENERATE PRINT/PDF
              </button>
            </div>
          </div>

          <div className="p-4 bg-gray-100 border-t border-[#222] text-[9px] flex justify-between items-center text-gray-500 font-bold uppercase tracking-widest leading-relaxed">
            <span>User: {user?.username}</span>
            <div className="flex items-center gap-1.5">
              <div className="w-2 h-2 rounded-full bg-green-500 animate-pulse" />
              <span>Auto-saved</span>
            </div>
          </div>
        </aside>
      </div>

      {/* Print View (Hidden in UI) */}
      <div id="print-area" className="hidden print:block bg-white text-[#222] min-h-screen">
        <PrintLayout quoteData={quoteData} totalBeforeGst={totalBeforeGst} gstValue={gstValue} grandTotal={grandTotal} />
      </div>

      {/* Modals --- */}
      <AnimatePresence>
        {isSavedQuotesModalOpen && (
          <Modal title="Saved Quotations" onClose={() => setIsSavedQuotesModalOpen(false)}>
            <div className="space-y-2 max-h-[60vh] overflow-y-auto pr-2">
              {savedQuotes.length === 0 ? (
                <div className="text-center py-12 text-gray-400">No saved quotes found.</div>
              ) : (
                savedQuotes.map(q => (
                  <div key={q.id} className="flex items-center justify-between p-4 border border-[#222] hover:bg-gray-50 transition-colors">
                    <div>
                      <h4 className="font-bold">{q.customerName || 'Unnamed Customer'}</h4>
                      <div className="text-xs text-gray-500 font-mono">
                        {q.quoteNumber} | {q.projectName} | {new Date(q.lastSaved).toLocaleDateString()}
                      </div>
                    </div>
                    <div className="flex gap-2">
                      <button onClick={() => loadSavedQuote(q)} className="p-2 hover:bg-[#ffd400] rounded transition-colors" title="Load">
                        <ChevronRight size={18} />
                      </button>
                      <button onClick={() => deleteSavedQuote(q.id)} className="p-2 hover:bg-red-100 text-red-500 rounded transition-colors" title="Delete">
                        <Trash2 size={18} />
                      </button>
                    </div>
                  </div>
                ))
              )}
            </div>
          </Modal>
        )}

        {isChangePassModalOpen && (
          <Modal title="Security Settings" onClose={() => setIsChangePassModalOpen(false)}>
            <div className="space-y-4">
              <div className="space-y-2">
                <label className="text-[10px] font-black uppercase text-gray-400 tracking-widest">New Operator Password</label>
                <input 
                  type="password" 
                  className="w-full p-4 border-2 border-[#222] focus:bg-[#f3f4f6] outline-none transition-colors font-bold text-sm" 
                  onKeyDown={e => {
                    if (e.key === 'Enter') {
                      const newPass = (e.target as HTMLInputElement).value;
                      if (newPass && user) {
                        const updatedUser = { ...user, pass: newPass };
                        setUser(updatedUser);
                        localStorage.setItem('dwaram_user', JSON.stringify(updatedUser));
                        setIsChangePassModalOpen(false);
                        alert("Password updated!");
                      }
                    }
                  }}
                />
              </div>
              <p className="text-[9px] text-gray-400 font-bold uppercase tracking-widest">Press [ENTER] to COMMIT changes.</p>
            </div>
          </Modal>
        )}
      </AnimatePresence>
    </div>
  );
}

// --- Sub-components ---

function LoginView({ onLogin }: { onLogin: (u: string, p: string) => void }) {
  const [username, setUsername] = useState("");
  const [password, setPassword] = useState("");

  return (
    <div className="min-h-screen bg-[#222] flex items-center justify-center p-6 bg-[radial-gradient(circle_at_1px_1px,#333_1px,transparent_0)] bg-[size:40px_40px]">
      <motion.div 
        initial={{ opacity: 0, scale: 0.9 }}
        animate={{ opacity: 1, scale: 1 }}
        className="w-full max-w-md bg-white border-2 border-[#222] shadow-[8px_8px_0px_0px_rgba(255,212,0,1)] overflow-hidden"
      >
        <div className="bg-[#ffd400] p-8 text-center border-b-2 border-[#222]">
          <div className="inline-flex items-center justify-center mb-6">
            <Logo color="#222" size={80} />
          </div>
          <h1 className="text-3xl font-black uppercase tracking-tighter leading-none">Dwaram Interiors</h1>
          <p className="text-[10px] uppercase font-bold tracking-[0.3em] mt-2 opacity-60">Authentication Proxy</p>
        </div>
        
        <div className="p-8 space-y-6 bg-white">
          <div className="space-y-4">
            <div className="space-y-1">
              <label className="text-[10px] font-black uppercase text-gray-400 tracking-widest">Operator Username</label>
              <input 
                autoFocus
                className="w-full p-4 border-2 border-[#222] focus:bg-[#f3f4f6] outline-none transition-colors font-bold uppercase text-sm"
                value={username}
                onChange={e => setUsername(e.target.value)}
              />
            </div>
            <div className="space-y-1">
              <label className="text-[10px] font-black uppercase text-gray-400 tracking-widest">Access Credentials</label>
              <input 
                type="password"
                className="w-full p-4 border-2 border-[#222] focus:bg-[#f3f4f6] outline-none transition-colors font-bold text-sm"
                value={password}
                onChange={e => setPassword(e.target.value)}
                onKeyDown={e => e.key === 'Enter' && onLogin(username, password)}
              />
            </div>
          </div>
          
          <button 
            onClick={() => onLogin(username, password)}
            className="w-full bg-[#222] text-white py-5 font-black uppercase tracking-[0.2em] text-xs hover:bg-black transition-all shadow-[4px_4px_0px_#ffd400]"
          >
            ESTABLISH SESSION
          </button>
          
          <div className="pt-4 text-center">
            <p className="text-[9px] text-gray-300 font-bold uppercase tracking-[0.2em]">
              Authorized Personnel Only beyond this point
            </p>
          </div>
        </div>
      </motion.div>
    </div>
  );
}

function HeaderInput({ label, value, onChange, type = "text" }: { 
  label: string, value: string, onChange: (v: string) => void, type?: string 
}) {
  return (
    <div className="flex flex-col">
      <label className="text-[9px] font-black uppercase text-[#222]/50 mb-1 tracking-wider">{label}</label>
      <input 
        type={type}
        className="w-full h-8! border-[#222]! border-2! bg-white hover:bg-gray-50 focus:bg-white outline-none font-bold text-xs shadow-[1px_1px_0px_#222] transition-all"
        value={value}
        onChange={e => onChange(e.target.value)}
      />
    </div>
  );
}

function ActionButton({ icon, label, onClick, variant = 'light' }: { 
  icon: any, label: string, onClick: () => void, variant?: 'light' | 'dark' 
}) {
  const isDark = variant === 'dark';
  return (
    <button 
      onClick={onClick}
      className={`flex items-center gap-2 px-4 py-3 rounded text-sm font-bold transition-all shadow-sm active:scale-95 ${
        isDark ? 'bg-[#222] text-[#ffd400] hover:bg-black' : 'bg-white border border-[#222] hover:bg-gray-50'
      }`}
    >
      <span className="shrink-0">{icon}</span>
      <span className="truncate">{label}</span>
    </button>
  );
}

function Modal({ title, children, onClose }: { title: string, children: ReactNode, onClose: () => void }) {
  return (
    <div className="fixed inset-0 z-50 flex items-center justify-center p-6">
      <motion.div 
        initial={{ opacity: 0 }} 
        animate={{ opacity: 1 }} 
        exit={{ opacity: 0 }} 
        className="absolute inset-0 bg-black/60 backdrop-blur-sm"
        onClick={onClose}
      />
      <motion.div 
        initial={{ opacity: 0, scale: 0.9, y: 20 }}
        animate={{ opacity: 1, scale: 1, y: 0 }}
        exit={{ opacity: 0, scale: 0.9, y: 20 }}
        className="relative w-full max-w-2xl bg-white border border-[#222] shadow-[8px_8px_0px_0px_rgba(34,34,34,1)] flex flex-col"
      >
        <div className="p-4 border-b border-[#222] bg-[#ffd400] flex items-center justify-between">
          <h3 className="font-black uppercase tracking-tight">{title}</h3>
          <button onClick={onClose} className="p-1 hover:bg-black/10 rounded">
            <X size={20} />
          </button>
        </div>
        <div className="p-6">
          {children}
        </div>
      </motion.div>
    </div>
  );
}

// --- Print Layout Components ---

function PrintLayout({ quoteData, totalBeforeGst, gstValue, grandTotal }: { 
  quoteData: QuoteData, totalBeforeGst: number, gstValue: number, grandTotal: number 
}) {
  return (
    <div className="print-view">
      {/* Page 1: Greeting */}
      <div className="page page-break relative min-h-screen p-12 flex flex-col">
        <header className="flex justify-between items-start mb-12">
          <div className="flex items-center gap-4">
            <Logo color="#222" size={60} />
            <div>
              <h1 className="text-3xl font-black uppercase tracking-tighter leading-none">Dwaram Interiors</h1>
              <p className="text-[10px] uppercase font-bold tracking-[0.2em] mt-1">Design Perfection Studio</p>
            </div>
          </div>
          <div className="text-right flex flex-col gap-1">
            <p className="text-xs font-bold uppercase">Contact: +91 99999 99999</p>
            <p className="text-xs text-gray-500 font-mono">REF: {quoteData.quoteNumber}</p>
            <p className="text-xs text-gray-500">{new Date(quoteData.date).toLocaleDateString(undefined, { dateStyle: 'long' })}</p>
          </div>
        </header>

        <div className="h-0.5 bg-[#222] mb-8" />

        <section className="bg-gray-50 p-6 mb-12 border border-gray-100">
          <div className="grid grid-cols-2 gap-12">
            <div>
              <LabelValue label="CUSTOMER NAME" value={quoteData.customerName} />
              <LabelValue label="PROJECT SITE" value={quoteData.projectName} />
            </div>
            <div>
              <LabelValue label="ESTIMATED BY" value={quoteData.createdBy} />
              <LabelValue label="LOCATION" value="Hyderabad, Telangana" />
            </div>
          </div>
        </section>

        <section className="flex-1 space-y-12">
          <div className="space-y-4 max-w-3xl">
            <h2 className="text-4xl font-light italic">Dear {quoteData.customerName || 'Customer'},</h2>
            <p className="text-lg leading-relaxed text-gray-700">
              Thank you for choosing Dwaram Interiors for your space transformation. We are thrilled to present this initial design proposal and cost estimation for your project at <span className="font-bold underline decoration-[#ffd400] underline-offset-4">{quoteData.projectName}</span>.
            </p>
            <p className="text-gray-500 leading-relaxed font-light">
              Our team has carefully analyzed your requirements to craft a balance between aesthetic luxury and functional ergonomics. This document outlines the room-wise breakdown of our bespoke interior solutions.
            </p>
          </div>
          
          <div className="grid grid-cols-2 md:grid-cols-4 gap-4 mt-8">
            <USPBox icon={<CheckCircle2 className="text-[#ffd400]" />} title="Premium Finish" desc="We use high-grade high-gloss and matte finishes." />
            <USPBox icon={<ShieldCheck className="text-[#ffd400]" />} title="Warranty" desc="10-year comprehensive warranty on all woodwork." />
            <USPBox icon={<Clock className="text-[#ffd400]" />} title="Timely Delivery" desc="Strict 45-day handover from site clearance." />
            <USPBox icon={<Maximize2 className="text-[#ffd400]" />} title="Space-Efficient" desc="Maximizing every inch of your floor plan." />
          </div>
        </section>

        <footer className="mt-auto pt-8 border-t border-gray-100 flex justify-between items-end">
          <div className="space-y-1">
            <p className="text-[10px] font-bold text-gray-400 uppercase">Authorized Signatory</p>
            <div className="h-12 w-32 border-b border-gray-200" />
            <p className="text-xs font-bold font-mono">Dwaram Interiors Team</p>
          </div>
          <p className="text-[10px] text-gray-300 font-bold uppercase tracking-widest">Page 01</p>
        </footer>
      </div>

      {/* Room Pages */}
      {quoteData.rooms.filter(room => room.items.length > 0).map((room, index) => (
        <div key={room.id} className="page page-break min-h-screen p-12 flex flex-col bg-white">
          <header className="flex justify-between items-center mb-8 pb-4 border-b border-gray-100">
            <div className="flex items-center gap-3">
              <Logo color="#222" size={32} />
              <span className="text-xs font-black uppercase tracking-tight">Dwaram Interiors</span>
            </div>
            <div className="text-right">
              <span className="text-[10px] font-bold text-gray-400 uppercase tracking-widest">{quoteData.quoteNumber} | Page {String(index + 2).padStart(2, '0')}</span>
            </div>
          </header>

          <main className="flex-1 overflow-visible">
            <h3 className="text-2xl font-black uppercase tracking-tight mb-6 flex items-center gap-2">
              <span className="bg-[#ffd400] px-2">{room.name}</span>
              <span className="text-gray-200">Exotic Estimate</span>
            </h3>

            <table className="w-full border-collapse">
              <thead>
                <tr className="bg-[#222] text-white">
                  <th className="p-3 text-left w-10 text-[10px] font-bold uppercase">S.N</th>
                  <th className="p-3 text-left text-[10px] font-bold uppercase">Item Details & Specification</th>
                  <th className="p-3 text-center w-16 text-[10px] font-bold uppercase">W</th>
                  <th className="p-3 text-center w-16 text-[10px] font-bold uppercase">H</th>
                  <th className="p-3 text-center w-16 text-[10px] font-bold uppercase">UNIT</th>
                  <th className="p-3 text-right w-24 text-[10px] font-bold uppercase">RATE</th>
                  <th className="p-3 text-right w-32 text-[10px] font-bold uppercase">TOTAL</th>
                </tr>
              </thead>
              <tbody>
                {room.items.map((item, i) => (
                  <tr key={item.id} className="border-b border-[#222]">
                    <td className="p-3 font-mono text-[11px] align-top">{i + 1}</td>
                    <td className="p-3 align-top">
                      <div className="font-bold text-xs uppercase mb-1">{item.name}</div>
                      <div className="text-[10px] text-gray-500 leading-relaxed font-light whitespace-pre-wrap">{item.description}</div>
                    </td>
                    <td className="p-3 text-center font-mono text-[11px] align-top">{item.width}</td>
                    <td className="p-3 text-center font-mono text-[11px] align-top">{item.height || '--'}</td>
                    <td className="p-3 text-center font-mono text-[11px] align-top">{item.unit}</td>
                    <td className="p-3 text-right font-mono text-[11px] align-top">₹{parseFloat(item.rate).toLocaleString()}</td>
                    <td className="p-3 text-right font-black font-mono text-[11px] align-top bg-gray-50">
                      ₹{calculateItemTotal(item).toLocaleString()}
                    </td>
                  </tr>
                ))}
              </tbody>
              <tfoot>
                <tr className="bg-white border-t-2 border-[#222]">
                  <td colSpan={6} className="p-4 text-right text-xs font-black uppercase tracking-wider">Room Subtotal</td>
                  <td className="p-4 text-right font-black font-mono text-lg bg-[#ffd400]">
                    ₹{calculateRoomSubtotal(room).toLocaleString()}
                  </td>
                </tr>
              </tfoot>
            </table>
          </main>

          <footer className="mt-auto pt-4 border-t-2 border-[#222] flex justify-between items-center bg-gray-50 -mx-12 px-12 py-6">
            <div className="flex gap-8">
              <div className="flex items-center gap-2">
                <Logo color="#222" size={20} />
                <span className="text-[10px] font-black uppercase">Dwaram Interiors</span>
              </div>
              <p className="text-[10px] text-gray-500 font-bold">+91 99999 99999 | dwaraminteriors@gmail.com</p>
            </div>
            <p className="text-[10px] text-gray-400 font-mono uppercase">Room Ref: {room.id}</p>
          </footer>
        </div>
      ))}

      {/* Last Page: Summary */}
      <div className="page p-12 min-h-screen bg-white flex flex-col">
        <header className="mb-12 flex justify-between items-center border-b border-[#222] pb-6">
          <h2 className="text-4xl font-black uppercase tracking-tighter">Quotation Summary</h2>
          <Logo color="#222" size={50} />
        </header>

        <div className="flex-1">
          <div className="space-y-1 mb-8">
            <p className="text-[10px] font-bold text-gray-400 uppercase tracking-widest">Project Summary Breakdown</p>
            <div className="h-0.5 bg-[#ffd400]" />
          </div>

          <table className="w-full border-collapse mb-12">
            <thead>
              <tr className="bg-[#222] text-[#ffd400]">
                <th className="p-4 text-left font-black uppercase tracking-widest text-xs">Room / Work Area</th>
                <th className="p-4 text-right font-black uppercase tracking-widest text-xs">Amount Estimative</th>
              </tr>
            </thead>
            <tbody>
              {quoteData.rooms.filter(r => r.items.length > 0).map(room => (
                <tr key={room.id} className="border-b border-gray-100">
                  <td className="p-4 text-sm font-bold uppercase">{room.name}</td>
                  <td className="p-4 text-right font-mono font-bold">
                    ₹{calculateRoomSubtotal(room).toLocaleString()}
                  </td>
                </tr>
              ))}
              <tr className="bg-gray-50">
                <td className="p-4 text-sm font-black uppercase">Subtotal (Works)</td>
                <td className="p-4 text-right font-mono font-black text-xl">₹{totalBeforeGst.toLocaleString()}</td>
              </tr>
              {quoteData.isGstEnabled && (
                <tr className="border-b border-[#222]">
                  <td className="p-4 text-sm uppercase text-gray-500 italic">GST TAXATION (18%)</td>
                  <td className="p-4 text-right font-mono text-gray-500">₹{gstValue.toLocaleString()}</td>
                </tr>
              )}
            </tbody>
            <tfoot>
              <tr className="bg-[#ffd400] border-2 border-[#222]">
                <td className="p-6 text-xl font-black uppercase tracking-tighter">Grand Total Value</td>
                <td className="p-6 text-right font-black font-mono text-3xl">₹{grandTotal.toLocaleString()}</td>
              </tr>
            </tfoot>
          </table>

          <div className="grid grid-cols-2 gap-12 mt-12 bg-gray-50 p-8 border border-gray-100">
            <div className="space-y-4">
              <h4 className="text-xs font-black uppercase border-b border-[#222] pb-2 inline-block">General Terms</h4>
              <ul className="text-[10px] space-y-2 text-gray-500 list-disc pl-4 leading-relaxed">
                <li>Material will be as per standard industry specifications unless explicitly mentioned.</li>
                <li>Quote valid for 30 days from the date of issuance.</li>
                <li>Payment schedule: 50% Advance, 40% on Carcass completion, 10% on Finishing.</li>
                <li>Site cleaning and electricity are to be provided by the client.</li>
              </ul>
            </div>
            <div className="flex flex-col items-center justify-center border-l border-gray-200">
              <p className="text-[10px] font-bold text-gray-300 uppercase mb-8">Official Stamp & Signature</p>
              <div className="h-24 w-48 border border-dashed border-gray-200 flex items-center justify-center p-4">
                <Logo color="#f3f4f6" size={80} />
              </div>
            </div>
          </div>
        </div>

        <footer className="mt-12 text-center">
          <p className="text-[10px] font-bold text-gray-300 uppercase tracking-[.5em]">Thank You For Trusting Dwaram Interiors</p>
        </footer>
      </div>
    </div>
  );
}

function LabelValue({ label, value }: { label: string, value: string }) {
  return (
    <div className="mb-4">
      <div className="text-[10px] font-black text-gray-400 mb-1 tracking-widest">{label}</div>
      <div className="text-lg font-bold border-b-2 border-gray-200 pb-1 uppercase">{value || '---'}</div>
    </div>
  );
}

function USPBox({ icon, title, desc }: { icon: any, title: string, desc: string }) {
  return (
    <div className="p-4 border border-gray-100 rounded-xl bg-white shadow-sm flex flex-col items-center text-center group hover:border-[#ffd400] transition-colors">
      <div className="mb-3 transform group-hover:scale-110 transition-transform">{icon}</div>
      <div className="text-xs font-bold uppercase mb-1">{title}</div>
      <p className="text-[9px] text-gray-400 leading-tight">{desc}</p>
    </div>
  );
}

function Logo({ color, size }: { color: string, size: number }) {
  // We use SVG to represent the official Dwaram Interiors logo provided by the user
  return (
    <svg 
      width={size} 
      height={size} 
      viewBox="0 0 100 100" 
      fill="none" 
      xmlns="http://www.w3.org/2000/svg"
      style={{ minWidth: size }}
    >
      {/* The main 'D' shape / background arch */}
      <path 
        d="M24 12V88H50C70 88 88 70 88 50C88 30 70 12 50 12H24Z" 
        fill={YELLOW_ACCENT} 
        stroke="#222" 
        strokeWidth="1.5"
      />
      
      {/* Wardrobe on the left */}
      <rect x="30" y="15" width="22" height="70" fill="white" stroke="#222" strokeWidth="1" />
      <line x1="48" y1="40" x2="48" y2="60" stroke="#222" strokeWidth="1.5" />
      
      {/* Pendant Lamp */}
      <line x1="68" y1="12" x2="68" y2="30" stroke="#222" strokeWidth="0.8" />
      <path d="M58 42C58 35 78 35 78 42H58Z" fill="white" stroke="#222" strokeWidth="1" />
      <rect x="60" y="42" width="16" height="6" fill="white" stroke="#222" strokeWidth="1" />
      
      {/* Chest of Drawers */}
      <rect x="56" y="65" width="24" height="20" fill="white" stroke="#222" strokeWidth="1" />
      <line x1="60" y1="70" x2="76" y2="70" stroke="#222" strokeWidth="0.8" />
      <line x1="60" y1="75" x2="76" y2="75" stroke="#222" strokeWidth="0.8" />
      <line x1="60" y1="80" x2="76" y2="80" stroke="#222" strokeWidth="0.8" />
      
      {/* Accent strip on the far left */}
      <rect x="20" y="15" width="4" height="70" fill={YELLOW_ACCENT} />
    </svg>
  );
}
