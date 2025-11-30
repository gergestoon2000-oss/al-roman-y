import React, { useState, useEffect } from 'react';
import { 
  Edit3, 
  Check, 
  RotateCcw, 
  PlusCircle, 
  X, 
  ClipboardList, 
  Search, 
  CheckCircle2,
  Trash2 
} from 'lucide-react';

const CATEGORIES = {
    'نقل': ['نقل', 'السلاموني', 'د. بنز', 'فرج', 'سامبا', 'محمد. حنا'],
    'مرسيدس': ['نقل', 'السلاموني', 'د. بنز', 'فرج', 'سامبا'],
    'اسكانيا': ['نقل', 'السلاموني', 'د. بنز', 'فرج', 'سامبا', 'محمد. حنا'],
    'اكتروس': ['نقل', 'السلاموني', 'د. بنز', 'فرج', 'سامبا'],
    'مان': ['نقل', 'السلاموني', 'د. بنز', 'فرج', 'سامبا'],
    'ياباني': ['ياباني', 'ابراهيم', 'اشرف', 'ميوسكا', 'تايلاندي', 'فطيم'],
    'كهرباء': ['كهرباء', 'ياوا', 'البحار', 'بدر شاهين', 'ايهاب', 'الزغل', 'بنز 2'],
    'بلي': ['غير محدد'],
    'فلاتر': ['غير محدد'],
    'سيور': ['غير محدد'],
    'اويل سيل': ['غير محدد'],
    'العدد': ['غير محدد'],
    'اخري': ['غير محدد']
};

const BELT_OPTIONS = [
    '9.5', '9.5 م', '13', '13 م', '17', '17 م', '22', '22 م',
    '3PK', '4PK', '5PK', '6PK', '7PK', '8PK', '9PK', '10PK', '11PK', '12PK'
];

const BeltOptionModal = ({ onSelect, onClose }) => (
    <div className="fixed inset-0 bg-black/50 z-50 flex items-center justify-center p-4" dir="rtl">
        <div className="bg-white rounded-lg p-4 w-full max-w-sm shadow-xl">
            <h3 className="text-lg font-bold mb-3 text-center text-gray-800">اختر مقاس السير</h3>
            <div className="grid grid-cols-3 gap-2">
                {BELT_OPTIONS.map(opt => (
                    <button 
                        key={opt}
                        onClick={() => onSelect(opt)}
                        className="bg-blue-50 hover:bg-blue-100 text-blue-700 py-2 px-1 rounded text-sm font-semibold border border-blue-200 transition-colors"
                    >
                        {opt}
                    </button>
                ))}
            </div>
            <button onClick={onClose} className="mt-4 w-full bg-gray-200 hover:bg-gray-300 py-2 rounded text-gray-700 font-medium">إلغاء</button>
        </div>
    </div>
);

// مكون نافذة تأكيد الحذف
const ConfirmationModal = ({ item, onConfirm, onCancel }) => (
    <div className="fixed inset-0 bg-black/50 z-50 flex items-center justify-center p-4" dir="rtl">
        <div className="bg-white rounded-xl p-6 w-full max-w-sm shadow-2xl animate-in zoom-in duration-300">
            <h3 className="text-xl font-bold mb-3 text-red-700 text-center">تأكيد الحذف</h3>
            <p className="text-gray-700 text-right mb-5 leading-relaxed">
                هل أنت متأكد من حذف الصنف <span className="font-bold text-red-800">"{item.name}"</span> نهائياً؟ هذا الإجراء لا يمكن التراجع عنه.
            </p>
            <div className="flex justify-between gap-3">
                <button 
                    onClick={() => onConfirm(item)} 
                    className="flex-1 bg-red-600 hover:bg-red-700 text-white py-2 rounded-lg font-bold shadow-md transition-colors active:scale-95"
                >
                    نعم، احذف
                </button>
                <button 
                    onClick={onCancel} 
                    className="flex-1 bg-gray-200 hover:bg-gray-300 text-gray-700 py-2 rounded-lg font-medium transition-colors"
                >
                    إلغاء
                </button>
            </div>
        </div>
    </div>
);


// تم تعديل ItemCard لقبول setItemToDelete و setFilterAndView
const ItemCard = ({ item, editItem, toggleStatus, updateQuantity, setItemToDelete, setFilterAndView }) => (
    <div className="bg-white p-3 rounded-lg shadow-sm border border-gray-200 mb-3 relative animate-in fade-in zoom-in duration-300">
        
        {/* TOP SECTION: Flexbox Layout (Name/Date on Right | Qty/Edit on Left) */}
        <div className="flex justify-between items-start mb-2">
            
            {/* RIGHT SIDE (Primary Info): Item Name and Date/Time */}
            <div className="flex flex-col text-right flex-grow pr-2">
                <h3 className="text-lg font-bold text-red-800 leading-tight">{item.name}</h3>
                <div className="text-xs text-gray-500 flex justify-end gap-1 mt-0.5">
                    <span>{item.time}</span>
                    <span>-</span>
                    <span className="font-bold">{item.date}</span> {/* التاريخ الآن بخط سميك */}
                </div>
            </div>

            {/* LEFT SIDE (Action Info): Quantity Controls and Edit Button (Tightly packed) */}
            <div className="flex items-center space-x-1 space-x-reverse flex-shrink-0">
                {/* Quantity Controls (New Interactive Block) */}
                <div className="flex items-center bg-gray-100 rounded-full border border-gray-300 overflow-hidden">
                    
                    {/* Increment Button (+) - على اليمين */}
                    <button 
                        onClick={() => updateQuantity(item, 1)} 
                        className="text-green-600 hover:bg-green-100 p-1 transition-colors flex items-center justify-center h-full text-lg font-bold w-6"
                    >
                        +
                    </button>

                    {/* Quantity Display */}
                    <span className="text-gray-800 text-sm font-bold px-1.5 py-0.5 min-w-[20px] text-center">
                        {item.qty}
                    </span>
                    
                    {/* Decrement Button (-) - على اليسار */}
                    <button 
                        onClick={() => updateQuantity(item, -1)} 
                        className="text-red-600 hover:bg-red-100 p-1 transition-colors flex items-center justify-center h-full text-lg font-bold w-6"
                        disabled={parseInt(item.qty || '0', 10) <= 1} 
                    >
                        -
                    </button>
                </div>
                
                {/* Edit Button (Existing) */}
                <button onClick={() => editItem(item)} className="text-blue-500 hover:bg-blue-50 p-1 rounded transition-colors">
                    <Edit3 size={18} />
                </button>
            </div>
        </div>

        <div className="border-t border-dashed border-gray-200 my-2"></div>

        {/* Bottom Row - Rearranged: Category / Supplier / Action */}
        <div className="grid grid-cols-3 items-center gap-2">
            
            {/* 1. Right: Category (أقصى اليمين) - أصبح زر تفاعلي */}
            <div className="text-right">
                <button
                    onClick={() => setFilterAndView('category', item.category)}
                    className="text-xs font-medium text-blue-600 bg-blue-50 px-2 py-1 rounded truncate block hover:bg-blue-200 transition-colors"
                    title={`عرض كل أصناف ${item.category}`}
                >
                    {item.category}
                </button>
            </div>
            
            {/* 2. Center: Supplier - أصبح زر تفاعلي */}
            <div className="text-center">
                <button
                    onClick={() => setFilterAndView('supplier', item.supplier)}
                    className="text-xs font-semibold text-gray-600 bg-gray-50 px-2 py-1 rounded border border-gray-200 truncate block hover:bg-gray-200 transition-colors"
                    title={`عرض كل أصناف المورد ${item.supplier}`}
                >
                    {item.supplier}
                </button>
            </div>

            {/* 3. Left: Action Button (تأكيد الشراء / استرجاع / حذف) (أقصى اليسار) */}
            <div className="text-left">
                {item.status === 'shortage' ? (
                    <button 
                        onClick={() => toggleStatus(item)}
                        className="bg-green-600 hover:bg-green-700 text-white text-xs px-3 py-1.5 rounded shadow-sm flex items-center gap-1 transition-colors"
                    >
                        <Check size={14} />
                        <span className="mr-1">تأكيد الشراء</span>
                    </button>
                ) : (
                    // Purchased status: show Revert and Delete buttons
                    <div className="flex items-center space-x-2 space-x-reverse justify-end">
                        <button 
                            onClick={() => toggleStatus(item)}
                            className="bg-amber-600 hover:bg-amber-700 text-white text-xs px-3 py-1.5 rounded shadow-sm flex items-center gap-1 transition-colors"
                        >
                            <RotateCcw size={14} />
                            <span className="mr-1">استرجاع</span>
                        </button>
                        
                        {/* زر الحذف - يظهر فقط إذا تم تمرير setItemToDelete (لأنه يدل على الحاجة للحذف) */}
                        {setItemToDelete && (
                            <button
                                onClick={() => setItemToDelete(item)}
                                className="text-red-500 hover:text-white hover:bg-red-600 p-1.5 rounded-full transition-colors border border-red-500 flex-shrink-0"
                                title="حذف الصنف نهائياً"
                            >
                                <Trash2 size={16} />
                            </button>
                        )}
                    </div>
                )}
            </div>
        </div>
    </div>
);

export default function App() {
    // State
    const [view, setView] = useState('shortages'); // shortages, search, purchased
    const [items, setItems] = useState(() => {
        try {
            // Load data from localStorage
            const saved = localStorage.getItem('inventory_items');
            return saved ? JSON.parse(saved) : [];
        } catch (e) {
            return [];
        }
    });
    const [isFormOpen, setIsFormOpen] = useState(false);
    
    // Form State
    const [formData, setFormData] = useState({
        id: null,
        name: '',
        qty: '1', // Default quantity to '1'
        category: '',
        supplier: ''
    });

    // حالة الصنف المراد حذفه لتشغيل نافذة التأكيد
    const [itemToDelete, setItemToDelete] = useState(null); 
    
    // Search Filters
    const [filters, setFilters] = useState({ name: '', category: '', supplier: '' });

    // Special Input State
    const [showBeltModal, setShowBeltModal] = useState(false);

    // State for scroll buttons visibility
    const [showScrollButtons, setShowScrollButtons] = useState(false);

    // Persist Data
    useEffect(() => {
        // Save data to localStorage whenever items changes
        localStorage.setItem('inventory_items', JSON.stringify(items));
    }, [items]);

    // دالة جديدة لضبط الفلاتر وتغيير وضع العرض إلى البحث
    const setFilterAndView = (type, value) => {
        if (type === 'category') {
            setFilters({ name: '', category: value, supplier: '' });
        } else if (type === 'supplier') {
            setFilters({ name: '', category: '', supplier: value });
        }
        setView('search');
    };

    // --- Scroll Logic ---
    useEffect(() => {
        let timeout;

        const handleScroll = () => {
            // Show the buttons
            setShowScrollButtons(true);
            
            // Clear previous timeout to reset the timer
            clearTimeout(timeout);
            
            // Set a new timeout to hide buttons after 2 seconds (2000ms) of inactivity
            timeout = setTimeout(() => {
                setShowScrollButtons(false);
            }, 2000);
        };

        // Attach scroll listener to the window
        window.addEventListener('scroll', handleScroll);
        
        // Trigger handleScroll once on component mount to show buttons briefly
        handleScroll(); 

        // Cleanup function to remove listener and clear timeout on unmount
        return () => {
            window.removeEventListener('scroll', handleScroll);
            clearTimeout(timeout);
        };
    }, []);

    const scrollToTop = () => {
        window.scrollTo({ top: 0, behavior: 'smooth' });
    };

    const scrollToBottom = () => {
        window.scrollTo({ top: document.documentElement.scrollHeight, behavior: 'smooth' });
    };
    // --- End Scroll Logic ---

    // --- Item Management Logic ---
    const handleCategoryChange = (cat) => {
        const suppliers = CATEGORIES[cat];
        setFormData(prev => ({
            ...prev,
            category: cat,
            // Automatically select supplier if only one option exists
            supplier: suppliers.length === 1 ? suppliers[0] : ''
        }));
    };

    const handleNameChange = (e) => {
        let val = e.target.value;
        const prevVal = formData.name;
        
        // Logic for "سيور" (Belts) to trigger the size modal
        if (formData.category === 'سيور') {
            // Detect number + space
            if (val.length > prevVal.length && val.endsWith(' ') && /\d $/.test(val)) {
                setFormData(prev => ({ ...prev, name: val.trim() + ' × ' }));
                setShowBeltModal(true);
                return;
            }
        }

        // Logic for "اويل سيل" (Oil Seal) to add ' × ' after a number and space
        if (formData.category === 'اويل سيل') {
             if (val.length > prevVal.length && val.endsWith(' ') && /\d $/.test(val)) {
                setFormData(prev => ({ ...prev, name: val.trim() + ' × ' }));
                return;
            }
        }

        setFormData(prev => ({ ...prev, name: val }));
    };

    const handleBeltSelect = (option) => {
        setFormData(prev => ({ ...prev, name: prev.name + option }));
        setShowBeltModal(false);
    };
    
    // Function to reset only item-specific fields (name, qty, id)
    const resetItemFields = () => {
        setFormData(prev => ({
            ...prev,
            id: null,
            name: '',
            qty: '1' // Resetting quantity to 1 for new entries
        }));
    };

    const saveItem = () => {
        // Simple validation
        if (!formData.name || !formData.category || !formData.supplier || parseInt(formData.qty) < 1) return;
        
        const now = new Date();
        const timestamp = now.getTime();
        const dateStr = now.toLocaleDateString('ar-EG');
        const timeStr = now.toLocaleTimeString('ar-EG', { hour: '2-digit', minute: '2-digit' });

        if (formData.id) {
            // Edit: Update item details
            setItems(prev => prev.map(item => item.id === formData.id ? { 
                ...item, 
                name: formData.name, 
                qty: formData.qty, 
                category: formData.category, 
                supplier: formData.supplier 
            } : item));
            
            closeForm(); // Close form after editing
        } else {
            // New: Create new item
            const newItem = {
                id: timestamp,
                name: formData.name,
                qty: formData.qty,
                category: formData.category,
                supplier: formData.supplier,
                date: dateStr, // Initial date/time of creation
                time: timeStr,
                status: 'shortage' 
            };
            // Add new item to the top of the list
            setItems(prev => [newItem, ...prev]);
            
            // Keep category and supplier selected, just reset name/qty/id for continuous entry
            resetItemFields(); 
        }
    };

    // دالة حذف الصنف
    const deleteItem = (itemToDelete) => {
        setItems(prev => prev.filter(item => item.id !== itemToDelete.id));
        setItemToDelete(null); // إغلاق نافذة التأكيد
    };

    const editItem = (item) => {
        setFormData({
            id: item.id,
            name: item.name,
            qty: item.qty,
            category: item.category,
            supplier: item.supplier
        });
        setIsFormOpen(true);
        if (view !== 'shortages') setView('shortages'); 
    };

    const closeForm = () => {
        // Reset ALL fields (category, supplier included) and close form
        setFormData({ id: null, name: '', qty: '1', category: '', supplier: '' });
        setIsFormOpen(false);
    };

    /**
     * Toggles the status of an item between 'shortage' and 'purchased' 
     * and updates the date/time to the moment of the status change.
     */
    const toggleStatus = (item) => {
        const now = new Date();
        // Use a consistent format for date and time strings
        const dateStr = now.toLocaleDateString('ar-EG'); 
        const timeStr = now.toLocaleTimeString('ar-EG', { hour: '2-digit', minute: '2-digit' });

        setItems(prev => prev.map(i => {
            if (i.id === item.id) {
                const newStatus = i.status === 'shortage' ? 'purchased' : 'shortage';
                
                // CRUCIAL: Update date and time upon *any* status change.
                return { 
                    ...i, 
                    status: newStatus, 
                    date: dateStr, 
                    time: timeStr 
                };
            }
            return i;
        }));
    };
    
    /**
     * Updates the quantity of an item directly from the ItemCard.
     * @param {object} item - The item object to update.
     * @param {number} delta - The change in quantity (+1 or -1).
     */
    const updateItemQuantity = (item, delta) => {
        setItems(prev => prev.map(i => {
            if (i.id === item.id) {
                const currentQty = parseInt(i.qty || '0', 10);
                let newQty = currentQty + delta;
                
                // Ensure quantity is at least 1
                if (newQty < 1) newQty = 1;

                // Return the updated item
                return { ...i, qty: String(newQty) };
            }
            return i;
        }));
    };

    // --- Filter Logic ---

    // 1. Calculate filtered items based on current view/filters
    const filteredItems = items.filter(item => {
        // يتم تطبيق فلاتر الاسم والفئة والمورد فقط في وضع البحث
        if (view === 'search') {
            const matchesName = item.name.toLowerCase().includes(filters.name.toLowerCase());
            const matchesCat = filters.category ? item.category === filters.category : true;
            const matchesSup = filters.supplier ? item.supplier === filters.supplier : true;
            return matchesName && matchesCat && matchesSup;
        } else {
            // في عرض النواقص/المشتريات، يتم التصفية حسب الحالة فقط
            return view === 'purchased' ? item.status === 'purchased' : item.status === 'shortage';
        }
    });

    return (
        <div className="min-h-screen bg-gray-100 font-sans" dir="rtl">
            <style>{`
                @import url('https://fonts.googleapis.com/css2?family=Tajawal:wght@400;500;700&display=swap');
                body { font-family: 'Tajawal', sans-serif; }
                /* Custom style to hide the number input spin buttons */
                input[type='number']::-webkit-inner-spin-button, 
                input[type='number']::-webkit-outer-spin-button { 
                    -webkit-appearance: none; 
                    margin: 0; 
                }
                input[type='number'] {
                    -moz-appearance: textfield;
                }
            `}</style>

            <div className="max-w-md mx-auto min-h-screen relative bg-gray-100 shadow-2xl">
                
                {/* Navigation (Fixed) */}
                <div className="sticky top-0 z-30 bg-white shadow-sm border-b border-gray-200 px-2 py-1 flex justify-around items-center">
                    
                    {/* 1. Shortages (Blue) */}
                    <button 
                        onClick={() => setView('shortages')}
                        className={`p-2 rounded-xl flex flex-col items-center transition-all text-xs w-1/3 ${view === 'shortages' ? 'text-blue-600' : 'text-gray-400 hover:text-blue-500'}`}
                    >
                        <ClipboardList size={24} className={`mb-1 ${view === 'shortages' ? 'bg-blue-100 p-1 rounded-full' : ''}`} />
                        <span className={`font-medium ${view === 'shortages' ? 'text-blue-700 font-bold' : ''}`}>قائمة النواقص</span>
                    </button>

                    {/* 2. Search (Purple) */}
                    <button 
                        onClick={() => {
                            // عند الانتقال إلى وضع البحث، لا تقم بمسح الفلاتر إذا كانت موجودة، فقط غيّر العرض
                            setView('search');
                        }}
                        className={`p-2 rounded-xl flex flex-col items-center transition-all text-xs w-1/3 ${view === 'search' ? 'text-purple-600' : 'text-gray-400 hover:text-purple-500'}`}
                    >
                        <Search size={24} className={`mb-1 ${view === 'search' ? 'bg-purple-100 p-1 rounded-full' : ''}`} />
                        <span className={`font-medium ${view === 'search' ? 'text-purple-700 font-bold' : ''}`}>بحث</span>
                    </button>

                    {/* 3. Purchased (Green) */}
                    <button 
                        onClick={() => setView('purchased')}
                        className={`p-2 rounded-xl flex flex-col items-center transition-all text-xs w-1/3 ${view === 'purchased' ? 'text-green-600' : 'text-gray-400 hover:text-green-500'}`}
                    >
                        <CheckCircle2 size={24} className={`mb-1 ${view === 'purchased' ? 'bg-green-100 p-1 rounded-full' : ''}`} />
                        <span className={`font-medium ${view === 'purchased' ? 'text-green-700 font-bold' : ''}`}>سجل المشتريات</span>
                    </button>
                </div>

                {/* Content */}
                <div className="p-4 pb-20">
                    
                    {/* View: Shortages */}
                    {view === 'shortages' && (
                        <div className="animate-in fade-in slide-in-from-bottom-2 duration-300">
                            {/* Form remains open continuously until user presses X */}
                            {isFormOpen && (
                                <div className="bg-white p-4 rounded-xl shadow-md border border-blue-100 mb-6 animate-in slide-in-from-top-4">
                                    <div className="flex justify-between items-center mb-4 border-b pb-2">
                                        <h2 className="font-bold text-gray-700">
                                            {formData.id ? 'تعديل الصنف' : 'إضافة نواقص'}
                                        </h2>
                                        <button onClick={closeForm} className="text-gray-400 hover:text-red-500 transition-colors">
                                            <X size={20} />
                                        </button>
                                    </div>
                                    
                                    <div className="space-y-3">
                                        
                                        {/* 1. Item Name (اسم الصنف) */}
                                        <div>
                                            <label className="block text-sm text-gray-600 mb-1">اسم الصنف</label>
                                            <input 
                                                type="text" 
                                                value={formData.name}
                                                onChange={handleNameChange}
                                                placeholder="اكتب اسم الصنف..."
                                                className="w-full p-2 border border-gray-300 rounded focus:border-blue-500 outline-none transition-colors"
                                                autoFocus
                                                autoComplete="off"
                                            />
                                            {formData.category === 'سيور' && (
                                                <p className="text-[11px] text-blue-500 mt-1 bg-blue-50 p-1 rounded inline-block">
                                                    تلميح: اكتب رقم ثم مسافة لإظهار المقاسات
                                                </p>
                                            )}
                                        </div>

                                        {/* 2. Quantity (الكمية) - With + and - buttons (عكس الترتيب هنا) */}
                                        <div>
                                            <label className="block text-sm text-gray-600 mb-1">الكمية</label>
                                            <div className="flex w-full border border-gray-300 rounded overflow-hidden focus-within:border-blue-500 transition-colors">
                                                
                                                {/* Increment Button (+) */}
                                                <button 
                                                    type="button"
                                                    onClick={() => {
                                                        const current = parseInt(formData.qty || '1', 10);
                                                        setFormData(prev => ({ ...prev, qty: String(current + 1) }));
                                                    }} 
                                                    className="bg-gray-100 hover:bg-gray-200 text-green-600 p-2 font-bold text-xl w-1/6 flex justify-center items-center"
                                                >
                                                    +
                                                </button>
                                                
                                                {/* Quantity Input Field */}
                                                <input 
                                                    type="number" 
                                                    value={formData.qty}
                                                    onChange={(e) => {
                                                        const val = Math.max(1, parseInt(e.target.value || '1', 10));
                                                        setFormData({...formData, qty: String(isNaN(val) ? 1 : val)});
                                                    }}
                                                    className="w-4/6 text-center p-2 outline-none border-none focus:ring-0"
                                                    min="1"
                                                />
                                                
                                                {/* Decrement Button (-) */}
                                                <button 
                                                    type="button"
                                                    onClick={() => {
                                                        const current = parseInt(formData.qty || '1', 10);
                                                        if (current > 1) {
                                                            setFormData(prev => ({ ...prev, qty: String(current - 1) }));
                                                        }
                                                    }} 
                                                    className="bg-gray-100 hover:bg-gray-200 text-red-600 p-2 font-bold text-xl w-1/6 flex justify-center items-center"
                                                >
                                                    -
                                                </button>
                                            </div>
                                        </div>

                                        {/* 3. Category (الفئة) */}
                                        <div>
                                            <label className="block text-sm text-gray-600 mb-1">الفئة</label>
                                            <select 
                                                value={formData.category} 
                                                onChange={(e) => handleCategoryChange(e.target.value)}
                                                className="w-full p-2 border border-gray-300 rounded bg-gray-50 focus:border-blue-500 outline-none transition-colors"
                                            >
                                                <option value="">اختر الفئة...</option>
                                                {Object.keys(CATEGORIES).map(cat => (
                                                    <option key={cat} value={cat}>{cat}</option>
                                                ))}
                                            </select>
                                        </div>

                                        {/* 4. Supplier (المورد) */}
                                        {formData.category && (
                                            <div className="animate-in fade-in duration-300">
                                                <label className="block text-sm text-gray-600 mb-1">المورد</label>
                                                <select 
                                                    value={formData.supplier} 
                                                    onChange={(e) => setFormData({...formData, supplier: e.target.value})}
                                                    className="w-full p-2 border border-gray-300 rounded bg-gray-50 focus:border-blue-500 outline-none transition-colors"
                                                    disabled={CATEGORIES[formData.category].length === 1}
                                                >
                                                    <option value="">اختر المورد...</option>
                                                    {CATEGORIES[formData.category].map(sup => (
                                                        <option key={sup} value={sup}>{sup}</option>
                                                    ))}
                                                </select>
                                            </div>
                                        )}

                                        <button 
                                            onClick={saveItem}
                                            className="w-full mt-4 bg-blue-600 hover:bg-blue-700 text-white py-2 rounded font-bold shadow transition-transform active:scale-95"
                                        >
                                            {formData.id ? 'حفظ التعديل' : 'حفظ الصنف الجديد'}
                                        </button>
                                    </div>
                                </div>
                            )}

                            {/* List of Shortage Items */}
                            <div className="space-y-1">
                                <h3 className="text-xl font-bold text-gray-800 mb-4 border-b border-gray-300 pb-1">قائمة النواقص</h3>
                                {filteredItems.length === 0 ? (
                                    <div className="text-center text-gray-400 py-10 animate-in fade-in duration-500">
                                        <ClipboardList className="mx-auto mb-2 opacity-50" size={48} />
                                        <p>لا يوجد نواقص مسجلة حالياً</p>
                                    </div>
                                ) : (
                                    filteredItems.map(item => (
                                        <ItemCard 
                                            key={item.id} 
                                            item={item} 
                                            editItem={editItem} 
                                            toggleStatus={toggleStatus} 
                                            updateQuantity={updateItemQuantity} 
                                            setFilterAndView={setFilterAndView}
                                            // لا نمرر setItemToDelete هنا لأن الحذف غير متاح في قائمة النواقص
                                        />
                                    ))
                                )}
                            </div>
                        </div>
                    )}

                    {/* View: Purchased */}
                    {view === 'purchased' && (
                        <div className="animate-in fade-in slide-in-from-bottom-2 duration-300 space-y-1">
                            <h3 className="text-xl font-bold text-gray-800 mb-4 border-b border-gray-300 pb-1">سجل المشتريات</h3>
                            {filteredItems.length === 0 ? (
                                <div className="text-center text-gray-400 py-10 animate-in fade-in duration-500">
                                    <CheckCircle2 className="mx-auto mb-2 opacity-50" size={48} />
                                    <p>سجل المشتريات فارغ</p>
                                </div>
                            ) : (
                                filteredItems.map(item => (
                                    <ItemCard 
                                        key={item.id} 
                                        item={item} 
                                        editItem={editItem} 
                                        toggleStatus={toggleStatus} 
                                        updateQuantity={updateItemQuantity} 
                                        setItemToDelete={setItemToDelete} // تمرير دالة إعداد الحذف
                                        setFilterAndView={setFilterAndView}
                                    />
                                ))
                            )}
                        </div>
                    )}

                    {/* View: Search */}
                    {view === 'search' && (
                        <div className="animate-in fade-in slide-in-from-bottom-2 duration-300">
                            {/* Search and Filters Box */}
                            <div className="bg-white p-4 rounded-xl shadow-sm mb-4 border border-gray-200">
                                <div className="relative mb-3">
                                    <Search className="absolute left-3 top-2.5 text-gray-400" size={20} />
                                    <input 
                                        type="text" 
                                        value={filters.name}
                                        onChange={(e) => setFilters({...filters, name: e.target.value})}
                                        placeholder="بحث باسم الصنف..."
                                        className="w-full pr-4 pl-10 py-2 bg-gray-50 border border-gray-200 rounded-lg focus:outline-none focus:border-blue-400 transition-colors"
                                    />
                                </div>
                                <div className="grid grid-cols-2 gap-2">
                                    <select 
                                        value={filters.category}
                                        onChange={(e) => setFilters({...filters, category: e.target.value})}
                                        className="text-sm p-2 bg-gray-50 border border-gray-200 rounded-lg"
                                    >
                                        <option value="">كل الفئات</option>
                                        {Object.keys(CATEGORIES).map(cat => (
                                            <option key={cat} value={cat}>{cat}</option>
                                        ))}
                                    </select>
                                    <select 
                                        value={filters.supplier}
                                        onChange={(e) => setFilters({...filters, supplier: e.target.value})}
                                        className="text-sm p-2 bg-gray-50 border border-gray-200 rounded-lg"
                                    >
                                        <option value="">كل الموردين</option>
                                        {[...new Set(Object.values(CATEGORIES).flat())].map(sup => (
                                            <option key={sup} value={sup}>{sup}</option>
                                        ))}
                                    </select>
                                </div>
                                {(filters.name || filters.category || filters.supplier) && (
                                    <div className="mt-3 border-t pt-2 border-gray-200">
                                        <button
                                            onClick={() => setFilters({ name: '', category: '', supplier: '' })}
                                            className="w-full text-center text-sm text-red-600 hover:text-red-700 font-medium py-1 transition-colors"
                                        >
                                            إلغاء فلاتر البحث
                                        </button>
                                    </div>
                                )}
                            </div>
                            
                            {/* 2. Display Results: Split into Shortage and Purchased */}
                            <div className="space-y-1">
                                {(() => {
                                    const shortageResults = filteredItems.filter(item => item.status === 'shortage');
                                    const purchasedResults = filteredItems.filter(item => item.status === 'purchased');
                                    const totalResults = shortageResults.length + purchasedResults.length;

                                    if (totalResults === 0) {
                                        return (
                                            <div className="text-center text-gray-400 py-10 animate-in fade-in duration-500">
                                                <ClipboardList className="mx-auto mb-2 opacity-50" size={48} />
                                                <p>لا يوجد أصناف مطابقة لنتائج البحث</p>
                                            </div>
                                        );
                                    }

                                    return (
                                        <>
                                            {/* Shortage Results */}
                                            {shortageResults.length > 0 && (
                                                <div className="mt-4">
                                                    <h3 className="text-lg font-bold text-gray-700 mb-3 border-b pb-1 border-gray-300">
                                                        نتائج البحث (نواقص)
                                                    </h3>
                                                    <div className="space-y-1">
                                                        {shortageResults.map(item => (
                                                            <ItemCard 
                                                                key={item.id} 
                                                                item={item} 
                                                                editItem={editItem} 
                                                                toggleStatus={toggleStatus} 
                                                                updateQuantity={updateItemQuantity} 
                                                                setFilterAndView={setFilterAndView}
                                                            />
                                                        ))}
                                                    </div>
                                                </div>
                                            )}

                                            {/* Purchased Results (Appears below shortage/search box) */}
                                            {purchasedResults.length > 0 && (
                                                <div className="mt-6 pt-4 border-t border-green-200 border-dashed">
                                                    <h3 className="text-lg font-bold text-green-700 mb-3 border-b pb-1 border-green-300">
                                                        سجل المشتريات المطابقة
                                                    </h3>
                                                    <div className="space-y-1">
                                                        {purchasedResults.map(item => (
                                                            <ItemCard 
                                                                key={item.id} 
                                                                item={item} 
                                                                editItem={editItem} 
                                                                toggleStatus={toggleStatus} 
                                                                updateQuantity={updateItemQuantity} 
                                                                setItemToDelete={setItemToDelete} // تمرير دالة إعداد الحذف
                                                                setFilterAndView={setFilterAndView}
                                                            />
                                                        ))}
                                                    </div>
                                                </div>
                                            )}
                                        </>
                                    );
                                })()}
                            </div>
                        </div>
                    )}
                </div>
                
                {/* Floating Action Button (FAB) for New Item (Bottom Right) */}
                {(view === 'shortages' && !isFormOpen) && (
                    <button
                        onClick={() => setIsFormOpen(true)}
                        className="fixed bottom-4 right-4 bg-blue-600 text-white p-4 rounded-full shadow-lg hover:bg-blue-700 transition-all z-40 active:scale-90"
                        title="إضافة بند جديد"
                    >
                        <PlusCircle size={28} />
                    </button>
                )}
                
                {/* Floating Scroll Buttons (Bottom Left) */}
                <div 
                    className={`fixed bottom-[7rem] left-4 z-40 flex flex-col space-y-2 transition-opacity duration-300 ${showScrollButtons ? 'opacity-60' : 'opacity-0 pointer-events-none'}`}
                >
                    {/* Scroll Up Button */}
                    <button
                        onClick={scrollToTop}
                        className="bg-purple-600 text-white p-3 rounded-full shadow-lg hover:bg-purple-700 transition-all active:scale-90"
                        title="أعلى الصفحة"
                    >
                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="M12 19V5M5 12l7-7 7 7"/></svg>
                    </button>
                    {/* Scroll Down Button */}
                    <button
                        onClick={scrollToBottom}
                        className="bg-purple-600 text-white p-3 rounded-full shadow-lg hover:bg-purple-700 transition-all active:scale-90"
                        title="أسفل الصفحة"
                    >
                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><path d="M12 5v14M19 12l-7 7-7-7"/></svg>
                    </button>
                </div>


                {/* Modals */}
                {showBeltModal && (
                    <BeltOptionModal 
                        onSelect={handleBeltSelect} 
                        onClose={() => setShowBeltModal(false)} 
                    />
                )}
                
                {/* نافذة تأكيد الحذف */}
                {itemToDelete && (
                    <ConfirmationModal
                        item={itemToDelete}
                        onConfirm={deleteItem}
                        onCancel={() => setItemToDelete(null)}
                    />
                )}
            </div>
        </div>
    );
}
