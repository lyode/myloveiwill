import React, { useState } from 'react';
import { Users, Home, Heart, Edit3, Trash2, Download, AlertCircle, Check, ArrowLeft, Plus } from 'lucide-react';

const WillBeneficiaryApp = () => {
  const [activeTab, setActiveTab] = useState('home');
  const [activeAssetCategory, setActiveAssetCategory] = useState('');
  
  const [personalInfo, setPersonalInfo] = useState({
    fullName: '',
    dateOfBirth: '',
    address: '',
    city: '',
    state: '',
    zipCode: '',
    phone: '',
    email: ''
  });

  const [beneficiaries, setBeneficiaries] = useState([]);
  const [assets, setAssets] = useState({
    properties: [],
    land: [],
    cars: [],
    shares: [],
    cash: [],
    goldSilver: [],
    diamonds: [],
    pearls: [],
    jewelry: [],
    art: [],
    electronics: [],
    furniture: [],
    collectibles: [],
    other: []
  });
  
  const [executors, setExecutors] = useState([]);
  const [specialInstructions, setSpecialInstructions] = useState('');
  const [isUnlocked, setIsUnlocked] = useState(false);
  const [userPin, setUserPin] = useState('');
  const [savedPin, setSavedPin] = useState('1234');
  const [showPinSetup, setShowPinSetup] = useState(false);
  const [tempPin, setTempPin] = useState('');
  const [confirmPin, setConfirmPin] = useState('');
  const [pinError, setPinError] = useState('');

  const [showBeneficiaryForm, setShowBeneficiaryForm] = useState(false);
  const [showAssetForm, setShowAssetForm] = useState(false);
  const [editingIndex, setEditingIndex] = useState(-1);

  const [newBeneficiary, setNewBeneficiary] = useState({
    name: '',
    relationship: '',
    dateOfBirth: '',
    address: '',
    phone: '',
    email: '',
    percentage: 0,
    lawyerCertified: false,
    certificationDate: '',
    lawyerName: ''
  });

  const [newAsset, setNewAsset] = useState({
    name: '',
    description: '',
    value: '',
    location: '',
    beneficiary: '',
    notes: '',
    lawyerCertified: false,
    certificationDate: '',
    lawyerName: ''
  });

  const assetCategories = {
    properties: { title: 'My House', icon: '🏠', description: 'Real estate properties' },
    land: { title: 'My Territory', icon: '🌍', description: 'Land and territories' },
    cars: { title: 'My Ferrari', icon: '🏎️', description: 'Vehicles and transportation' },
    shares: { title: 'Toilet Paper', icon: '🧻', description: 'Stocks and shares' },
    cash: { title: 'Cigar', icon: '🚬', description: 'Cash and currency' },
    goldSilver: { title: 'Screw Driver', icon: '🔧', description: 'Precious metals' },
    diamonds: { title: 'Diamonds', icon: '💎', description: 'Diamond collection' },
    pearls: { title: 'Stones', icon: '🪨', description: 'Pearls and precious stones' },
    jewelry: { title: 'My Jewels', icon: '💍', description: 'Jewelry collection' },
    art: { title: 'My Masterpieces', icon: '🎨', description: 'Art and collectibles' },
    electronics: { title: 'My Gadgets', icon: '📱', description: 'Electronics and devices' },
    furniture: { title: 'My Comfort', icon: '🛋️', description: 'Furniture and home items' },
    collectibles: { title: 'My Treasures', icon: '🏆', description: 'Collectibles and memorabilia' },
    other: { title: 'Other Valuables', icon: '💼', description: 'Other valuable items' }
  };

  const handlePinEntry = () => {
    if (userPin === savedPin) {
      setIsUnlocked(true);
      setUserPin('');
      setPinError('');
    } else {
      setPinError('Incorrect PIN. Please try again.');
      setUserPin('');
    }
  };

  const handlePinSetup = () => {
    if (tempPin.length < 4) {
      setPinError('PIN must be at least 4 digits');
      return;
    }
    if (tempPin !== confirmPin) {
      setPinError('PINs do not match');
      return;
    }
    setSavedPin(tempPin);
    setShowPinSetup(false);
    setTempPin('');
    setConfirmPin('');
    setPinError('');
    alert('PIN updated successfully!');
  };

  const lockPersonalInfo = () => {
    setIsUnlocked(false);
    setUserPin('');
    setPinError('');
  };

  const addBeneficiary = () => {
    if (editingIndex >= 0) {
      const updated = [...beneficiaries];
      updated[editingIndex] = newBeneficiary;
      setBeneficiaries(updated);
      setEditingIndex(-1);
    } else {
      setBeneficiaries([...beneficiaries, { ...newBeneficiary, id: Date.now() }]);
    }
    setNewBeneficiary({
      name: '',
      relationship: '',
      dateOfBirth: '',
      address: '',
      phone: '',
      email: '',
      percentage: 0,
      lawyerCertified: false,
      certificationDate: '',
      lawyerName: ''
    });
    setShowBeneficiaryForm(false);
  };

  const addAsset = () => {
    const category = activeAssetCategory;
    if (editingIndex >= 0) {
      const updated = [...assets[category]];
      updated[editingIndex] = newAsset;
      setAssets({...assets, [category]: updated});
      setEditingIndex(-1);
    } else {
      setAssets({
        ...assets,
        [category]: [...assets[category], { ...newAsset, id: Date.now() }]
      });
    }
    setNewAsset({
      name: '',
      description: '',
      value: '',
      location: '',
      beneficiary: '',
      notes: '',
      lawyerCertified: false,
      certificationDate: '',
      lawyerName: ''
    });
    setShowAssetForm(false);
  };

  const deleteBeneficiary = (index) => {
    setBeneficiaries(beneficiaries.filter((_, i) => i !== index));
  };

  const deleteAsset = (category, index) => {
    setAssets({
      ...assets,
      [category]: assets[category].filter((_, i) => i !== index)
    });
  };

  const editBeneficiary = (index) => {
    setNewBeneficiary(beneficiaries[index]);
    setEditingIndex(index);
    setShowBeneficiaryForm(true);
  };

  const editAsset = (category, index) => {
    setNewAsset(assets[category][index]);
    setEditingIndex(index);
    setActiveAssetCategory(category);
    setShowAssetForm(true);
  };

  const totalPercentage = beneficiaries.reduce((sum, b) => sum + Number(b.percentage || 0), 0);

  const generateWill = () => {
    const allAssets = Object.entries(assets).flatMap(([category, items]) => 
      items.map(item => ({...item, category: assetCategories[category].title}))
    );

    const willContent = `LAST WILL AND TESTAMENT

I, ${personalInfo.fullName}, of ${personalInfo.address}, ${personalInfo.city}, ${personalInfo.state} ${personalInfo.zipCode}, being of sound mind and memory, do hereby make, publish, and declare this to be my Last Will and Testament.

PERSONAL INFORMATION:
Full Name: ${personalInfo.fullName}
Date of Birth: ${personalInfo.dateOfBirth}
Address: ${personalInfo.address}, ${personalInfo.city}, ${personalInfo.state} ${personalInfo.zipCode}
Phone: ${personalInfo.phone}
Email: ${personalInfo.email}

BENEFICIARIES:
${beneficiaries.map((beneficiary, i) => `Beneficiary ${i + 1}:
Name: ${beneficiary.name}
Relationship: ${beneficiary.relationship}
Percentage of Estate: ${beneficiary.percentage}%
Address: ${beneficiary.address}
Phone: ${beneficiary.phone}
Email: ${beneficiary.email}
Lawyer Certified: ${beneficiary.lawyerCertified ? 'Yes' : 'No'}${beneficiary.lawyerCertified ? `
Certifying Lawyer: ${beneficiary.lawyerName || 'Not specified'}
Certification Date: ${beneficiary.certificationDate || 'Not specified'}` : ''}`).join('\n\n')}

MY VALUABLE POSSESSIONS:
${allAssets.map((asset, i) => `${asset.category} ${i + 1}:
Name: ${asset.name}
Description: ${asset.description}
Estimated Value: ${asset.value}
Location: ${asset.location}
Designated Beneficiary: ${asset.beneficiary}
Notes: ${asset.notes}
Lawyer Certified: ${asset.lawyerCertified ? 'Yes' : 'No'}${asset.lawyerCertified ? `
Certifying Lawyer: ${asset.lawyerName || 'Not specified'}
Certification Date: ${asset.certificationDate || 'Not specified'}` : ''}`).join('\n\n')}

SPECIAL INSTRUCTIONS:
${specialInstructions}

This will was created on ${new Date().toLocaleDateString()}.

Note: This document is for informational purposes only and should be reviewed by a qualified attorney before execution.`;

    const blob = new Blob([willContent], { type: 'text/plain' });
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = `${personalInfo.fullName || 'MyLoveWill'}_${new Date().toISOString().split('T')[0]}.txt`;
    a.click();
    URL.revokeObjectURL(url);
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-pink-300 via-purple-300 to-indigo-400">
      <div className="max-w-md mx-auto p-3 sm:max-w-6xl sm:p-6">
        <div className="mb-4 text-center bg-white/20 backdrop-blur-sm rounded-xl p-4 shadow-lg sm:mb-8 sm:p-6">
          <h1 className="text-2xl font-bold text-white mb-2 drop-shadow-lg sm:text-4xl">My Love & I Will</h1>
          <p className="text-white/90 text-sm drop-shadow sm:text-lg">Planning my legacy with love and care</p>
        </div>

        {/* Home Page */}
        {activeTab === 'home' && (
          <div className="bg-white/90 backdrop-blur-sm rounded-2xl p-6 shadow-xl border border-white/20 sm:p-8">
            <div className="text-center mb-8">
              <div className="text-6xl mb-4">💝</div>
              <h2 className="text-2xl font-bold text-gray-800 mb-4 bg-gradient-to-r from-pink-500 to-purple-600 bg-clip-text text-transparent">Welcome to Your Legacy</h2>
              <p className="text-gray-600">Create your will with love and organize everything that matters</p>
            </div>

            <div className="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-3">
              <button
                onClick={() => setActiveTab('aboutme')}
                className="bg-gradient-to-r from-pink-400 to-rose-500 text-white p-6 rounded-2xl shadow-lg hover:shadow-xl transform hover:scale-105 transition-all"
              >
                <div className="text-4xl mb-3">👤</div>
                <h3 className="text-lg font-bold mb-2">About Me</h3>
                <p className="text-sm opacity-90">My personal information</p>
              </button>

              <button
                onClick={() => setActiveTab('mythings')}
                className="bg-gradient-to-r from-purple-400 to-indigo-500 text-white p-6 rounded-2xl shadow-lg hover:shadow-xl transform hover:scale-105 transition-all"
              >
                <div className="text-4xl mb-3">💎</div>
                <h3 className="text-lg font-bold mb-2">My Things</h3>
                <p className="text-sm opacity-90">All my valuable possessions</p>
              </button>

              <button
                onClick={() => setActiveTab('beneficiaries')}
                className="bg-gradient-to-r from-green-400 to-emerald-500 text-white p-6 rounded-2xl shadow-lg hover:shadow-xl transform hover:scale-105 transition-all"
              >
                <div className="text-4xl mb-3">❤️</div>
                <h3 className="text-lg font-bold mb-2">My Loved Ones</h3>
                <p className="text-sm opacity-90">People I care about</p>
              </button>

              <button
                onClick={() => setActiveTab('executors')}
                className="bg-gradient-to-r from-blue-400 to-cyan-500 text-white p-6 rounded-2xl shadow-lg hover:shadow-xl transform hover:scale-105 transition-all"
              >
                <div className="text-4xl mb-3">⚖️</div>
                <h3 className="text-lg font-bold mb-2">Executors</h3>
                <p className="text-sm opacity-90">Who will handle my affairs</p>
              </button>

              <button
                onClick={() => setActiveTab('instructions')}
                className="bg-gradient-to-r from-orange-400 to-red-500 text-white p-6 rounded-2xl shadow-lg hover:shadow-xl transform hover:scale-105 transition-all"
              >
                <div className="text-4xl mb-3">📝</div>
                <h3 className="text-lg font-bold mb-2">My Wishes</h3>
                <p className="text-sm opacity-90">Special instructions & desires</p>
              </button>

              <button
                onClick={() => setActiveTab('review')}
                className="bg-gradient-to-r from-violet-400 to-purple-500 text-white p-6 rounded-2xl shadow-lg hover:shadow-xl transform hover:scale-105 transition-all"
              >
                <div className="text-4xl mb-3">📋</div>
                <h3 className="text-lg font-bold mb-2">Review & Create</h3>
                <p className="text-sm opacity-90">Generate my will document</p>
              </button>
            </div>
          </div>
        )}

        {/* About Me Page */}
        {activeTab === 'aboutme' && (
          <div className="bg-white/90 backdrop-blur-sm rounded-2xl p-4 shadow-xl border border-white/20 sm:p-8">
            <div className="flex items-center mb-6">
              <button
                onClick={() => setActiveTab('home')}
                className="mr-4 p-2 bg-gray-200 rounded-lg hover:bg-gray-300 transition-all"
              >
                <ArrowLeft size={20} />
              </button>
              <h2 className="text-xl font-bold text-gray-800 bg-gradient-to-r from-pink-500 to-purple-600 bg-clip-text text-transparent sm:text-3xl">About Me</h2>
              <button
                onClick={() => setShowPinSetup(!showPinSetup)}
                className="ml-auto flex items-center space-x-2 bg-gradient-to-r from-gray-500 to-gray-600 text-white px-4 py-2 rounded-xl hover:from-gray-600 hover:to-gray-700 transform hover:scale-105 transition-all shadow-lg text-sm"
              >
                <span>⚙️</span>
                <span>Setup PIN</span>
              </button>
            </div>
            
            {showPinSetup && (
              <div className="bg-gradient-to-r from-yellow-50 to-orange-50 p-4 rounded-xl border-2 border-yellow-300 mb-4 shadow-lg">
                <h3 className="text-lg font-semibold text-gray-800 mb-3 flex items-center">
                  <span className="mr-2">🔐</span>
                  Setup New PIN
                </h3>
                <div className="grid grid-cols-1 gap-3 sm:grid-cols-2">
                  <input
                    type="password"
                    placeholder="Enter new PIN (min 4 digits)"
                    value={tempPin}
                    onChange={(e) => setTempPin(e.target.value.replace(/\D/g, '').slice(0, 8))}
                    className="p-3 border-2 border-yellow-300 rounded-xl focus:ring-4 focus:ring-yellow-400 focus:border-yellow-500 bg-white/70 backdrop-blur-sm transition-all text-sm sm:text-base"
                    maxLength="8"
                  />
                  <input
                    type="password"
                    placeholder="Confirm new PIN"
                    value={confirmPin}
                    onChange={(e) => setConfirmPin(e.target.value.replace(/\D/g, '').slice(0, 8))}
                    className="p-3 border-2 border-yellow-300 rounded-xl focus:ring-4 focus:ring-yellow-400 focus:border-yellow-500 bg-white/70 backdrop-blur-sm transition-all text-sm sm:text-base"
                    maxLength="8"
                  />
                </div>
                {pinError && (
                  <p className="text-red-600 text-sm mt-2 font-medium">{pinError}</p>
                )}
                <div className="flex flex-col space-y-2 mt-3 sm:flex-row sm:space-y-0 sm:space-x-2">
                  <button
                    onClick={handlePinSetup}
                    className="bg-gradient-to-r from-green-500 to-emerald-600 text-white px-4 py-2 rounded-xl hover:from-green-600 hover:to-emerald-700 transform hover:scale-105 transition-all shadow-lg font-semibold text-sm sm:text-base"
                  >
                    Save PIN
                  </button>
                  <button
                    onClick={() => {
                      setShowPinSetup(false);
                      setTempPin('');
                      setConfirmPin('');
                      setPinError('');
                    }}
                    className="bg-gradient-to-r from-gray-400 to-gray-600 text-white px-4 py-2 rounded-xl hover:from-gray-500 hover:to-gray-700 transform hover:scale-105 transition-all shadow-lg font-semibold text-sm sm:text-base"
                  >
                    Cancel
                  </button>
                </div>
              </div>
            )}
            
            {!isUnlocked ? (
              <div className="text-center py-12">
                <div className="bg-gradient-to-r from-blue-100 to-purple-100 rounded-xl p-8 border-2 border-blue-300 max-w-md mx-auto">
                  <div className="text-6xl mb-4">🔐</div>
                  <h3 className="text-lg font-semibold text-gray-700 mb-4">Personal Information Locked</h3>
                  <p className="text-gray-600 text-sm mb-6">Enter your PIN to access personal details</p>
                  
                  <div className="space-y-4">
                    <input
                      type="password"
                      placeholder="Enter PIN"
                      value={userPin}
                      onChange={(e) => setUserPin(e.target.value.replace(/\D/g, '').slice(0, 8))}
                      onKeyPress={(e) => e.key === 'Enter' && handlePinEntry()}
                      className="w-full p-4 border-2 border-blue-300 rounded-xl focus:ring-4 focus:ring-blue-400 focus:border-blue-500 bg-white/70 backdrop-blur-sm transition-all text-center text-lg font-mono tracking-widest"
                      maxLength="8"
                      autoFocus
                    />
                    
                    {pinError && (
                      <p className="text-red-600 text-sm font-medium">{pinError}</p>
                    )}
                    
                    <button
                      onClick={handlePinEntry}
                      className="w-full bg-gradient-to-r from-blue-500 to-purple-600 text-white px-6 py-3 rounded-xl hover:from-blue-600 hover:to-purple-700 transform hover:scale-105 transition-all shadow-lg font-semibold"
                    >
                      🔓 Unlock
                    </button>
                    
                    <div className="text-xs text-gray-500 mt-4">
                      <p>Default PIN: 1234</p>
                      <p>Use "Setup PIN" to change it</p>
                    </div>
                  </div>
                </div>
              </div>
            ) : (
              <div>
                <div className="flex justify-end mb-4">
                  <button
                    onClick={lockPersonalInfo}
                    className="flex items-center space-x-2 bg-gradient-to-r from-red-500 to-pink-600 text-white px-4 py-2 rounded-xl hover:from-red-600 hover:to-pink-700 transform hover:scale-105 transition-all shadow-lg text-sm"
                  >
                    <span>🔒</span>
                    <span>Lock Info</span>
                  </button>
                </div>
                
                <div className="grid grid-cols-1 gap-3 sm:grid-cols-2 sm:gap-4">
                  <input
                    type="text"
                    placeholder="Full Legal Name"
                    value={personalInfo.fullName}
                    onChange={(e) => setPersonalInfo({...personalInfo, fullName: e.target.value})}
                    className="p-3 border-2 border-purple-200 rounded-xl focus:ring-4 focus:ring-purple-300 focus:border-purple-400 bg-white/50 backdrop-blur-sm transition-all hover:bg-white/70 text-sm sm:p-4 sm:text-base"
                  />
                  <input
                    type="date"
                    placeholder="Date of Birth"
                    value={personalInfo.dateOfBirth}
                    onChange={(e) => setPersonalInfo({...personalInfo, dateOfBirth: e.target.value})}
                    className="p-3 border-2 border-purple-200 rounded-xl focus:ring-4 focus:ring-purple-300 focus:border-purple-400 bg-white/50 backdrop-blur-sm transition-all hover:bg-white/70 text-sm sm:p-4 sm:text-base"
                  />
                  <input
                    type="text"
                    placeholder="Street Address"
                    value={personalInfo.address}
                    onChange={(e) => setPersonalInfo({...personalInfo, address: e.target.value})}
                    className="p-3 border-2 border-purple-200 rounded-xl focus:ring-4 focus:ring-purple-300 focus:border-purple-400 bg-white/50 backdrop-blur-sm transition-all hover:bg-white/70 text-sm sm:p-4 sm:text-base"
                  />
                  <input
                    type="text"
                    placeholder="City"
                    value={personalInfo.city}
                    onChange={(e) => setPersonalInfo({...personalInfo, city: e.target.value})}
                    className="p-3 border-2 border-purple-200 rounded-xl focus:ring-4 focus:ring-purple-300 focus:border-purple-400 bg-white/50 backdrop-blur-sm transition-all hover:bg-white/70 text-sm sm:p-4 sm:text-base"
                  />
                  <input
                    type="text"
                    placeholder="State"
                    value={personalInfo.state}
                    onChange={(e) => setPersonalInfo({...personalInfo, state: e.target.value})}
                    className="p-3 border-2 border-purple-200 rounded-xl focus:ring-4 focus:ring-purple-300 focus:border-purple-400 bg-white/50 backdrop-blur-sm transition-all hover:bg-white/70 text-sm sm:p-4 sm:text-base"
                  />
                  <input
                    type="text"
                    placeholder="ZIP Code"
                    value={personalInfo.zipCode}
                    onChange={(e) => setPersonalInfo({...personalInfo, zipCode: e.target.value})}
                    className="p-3 border-2 border-purple-200 rounded-xl focus:ring-4 focus:ring-purple-300 focus:border-purple-400 bg-white/50 backdrop-blur-sm transition-all hover:bg-white/70 text-sm sm:p-4 sm:text-base"
                  />
                  <input
                    type="tel"
                    placeholder="Phone Number"
                    value={personalInfo.phone}
                    onChange={(e) => setPersonalInfo({...personalInfo, phone: e.target.value})}
                    className="p-3 border-2 border-purple-200 rounded-xl focus:ring-4 focus:ring-purple-300 focus:border-purple-400 bg-white/50 backdrop-blur-sm transition-all hover:bg-white/70 text-sm sm:p-4 sm:text-base"
                  />
                  <input
                    type="email"
                    placeholder="Email Address"
                    value={personalInfo.email}
                    onChange={(e) => setPersonalInfo({...personalInfo, email: e.target.value})}
                    className="p-3 border-2 border-purple-200 rounded-xl focus:ring-4 focus:ring-purple-300 focus:border-purple-400 bg-white/50 backdrop-blur-sm transition-all hover:bg-white/70 text-sm sm:p-4 sm:text-base"
                  />
                </div>
              </div>
            )}
          </div>
        )}

        {/* My Things Page */}
        {activeTab === 'mythings' && !activeAssetCategory && (
          <div className="bg-white/90 backdrop-blur-sm rounded-2xl p-4 shadow-xl border border-white/20 sm:p-8">
            <div className="flex items-center mb-6">
              <button
                onClick={() => setActiveTab('home')}
                className="mr-4 p-2 bg-gray-200 rounded-lg hover:bg-gray-300 transition-all"
              >
                <ArrowLeft size={20} />
              </button>
              <h2 className="text-xl font-bold text-gray-800 bg-gradient-to-r from-purple-500 to-indigo-600 bg-clip-text text-transparent sm:text-3xl">My Things</h2>
            </div>

            <div className="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-3">
              {Object.entries(assetCategories).map(([key, category]) => (
                <button
                  key={key}
                  onClick={() => setActiveAssetCategory(key)}
                  className="bg-gradient-to-r from-white to-purple-50 p-6 rounded-2xl border-2 border-purple-200 shadow-lg hover:shadow-xl transform hover:scale-105 transition-all text-left"
                >
                  <div className="text-4xl mb-3">{category.icon}</div>
                  <h3 className="text-lg font-bold mb-2 text-gray-800">{category.title}</h3>
                  <p className="text-sm text-gray-600 mb-2">{category.description}</p>
                  <p className="text-xs text-blue-600 font-medium">
                    {assets[key].length} items
                  </p>
                </button>
              ))}
            </div>
          </div>
        )}

        {/* Individual Asset Category Page */}
        {activeTab === 'mythings' && activeAssetCategory && (
          <div className="bg-white/90 backdrop-blur-sm rounded-2xl p-4 shadow-xl border border-white/20 sm:p-8">
            <div className="flex items-center mb-6">
              <button
                onClick={() => setActiveAssetCategory('')}
                className="mr-4 p-2 bg-gray-200 rounded-lg hover:bg-gray-300 transition-all"
              >
                <ArrowLeft size={20} />
              </button>
              <div className="flex items-center">
                <span className="text-3xl mr-3">{assetCategories[activeAssetCategory].icon}</span>
                <h2 className="text-xl font-bold text-gray-800 bg-gradient-to-r from-purple-500 to-indigo-600 bg-clip-text text-transparent sm:text-3xl">
                  {assetCategories[activeAssetCategory].title}
                </h2>
              </div>
              <button
                onClick={() => setShowAssetForm(true)}
                className="ml-auto flex items-center space-x-2 bg-gradient-to-r from-green-500 to-blue-600 text-white px-4 py-2 rounded-xl hover:from-green-600 hover:to-blue-700 transform hover:scale-105 transition-all shadow-lg text-sm"
              >
                <Plus size={16} />
                <span>Add Item</span>
              </button>
            </div>

            {showAssetForm && (
              <div className="bg-gradient-to-r from-white to-purple-50 p-4 rounded-xl border-2 border-purple-200 mb-6 shadow-lg">
                <h3 className="text-lg font-semibold mb-3">{editingIndex >= 0 ? 'Edit' : 'Add'} {assetCategories[activeAssetCategory].title}</h3>
                <div className="grid grid-cols-1 gap-3 sm:grid-cols-2">
                  <input
                    type="text"
                    placeholder="Item Name"
                    value={newAsset.name}
                    onChange={(e) => setNewAsset({...newAsset, name: e.target.value})}
                    className="p-3 border-2 border-purple-200 rounded-xl focus:ring-4 focus:ring-purple-300 focus:border-purple-400 bg-white/70 backdrop-blur-sm transition-all text-sm sm:text-base"
                  />
                  <input
                    type="text"
                    placeholder="Description"
                    value={newAsset.description}
                    onChange={(e) => setNewAsset({...newAsset, description: e.target.value})}
                    className="p-3 border-2 border-purple-200 rounded-xl focus:ring-4 focus:ring-purple-300 focus:border-purple-400 bg-white/70 backdrop-blur-sm transition-all text-sm sm:text-base"
                  />
                  <input
                    type="number"
                    placeholder="Estimated Value ($)"
                    value={newAsset.value}
                    onChange={(e) => setNewAsset({...newAsset, value: e.target.value})}
                    className="p-3 border-2 border-purple-200 rounded-xl focus:ring-4 focus:ring-purple-300 focus:border-purple-400 bg-white/70 backdrop-blur-sm transition-all text-sm sm:text-base"
                  />
                  <input
                    type="text"
                    placeholder="Location"
                    value={newAsset.location}
                    onChange={(e) => setNewAsset({...newAsset, location: e.target.value})}
                    className="p-3 border-2 border-purple-200 rounded-xl focus:ring-4 focus:ring-purple-300 focus:border-purple-400 bg-white/70 backdrop-blur-sm transition-all text-sm sm:text-base"
                  />
                  <select
                    value={newAsset.beneficiary}
                    onChange={(e) => setNewAsset({...newAsset, beneficiary: e.target.value})}
                    className="p-3 border-2 border-purple-200 rounded-xl focus:ring-4 focus:ring-purple-300 focus:border-purple-400 bg-white/70 backdrop-blur-sm transition-all text-sm sm:text-base"
                  >
                    <option value="">Select Beneficiary (Optional)</option>
                    {beneficiaries.map((beneficiary, index) => (
                      <option key={index} value={beneficiary.name}>{beneficiary.name}</option>
                    ))}
                  </select>
                  <textarea
                    placeholder="Additional Notes"
                    value={newAsset.notes}
                    onChange={(e) => setNewAsset({...newAsset, notes: e.target.value})}
                    className="p-3 border-2 border-purple-200 rounded-xl focus:ring-4 focus:ring-purple-300 focus:border-purple-400 bg-white/70 backdrop-blur-sm transition-all text-sm sm:text-base sm:col-span-2"
                    rows="3"
                  />
                </div>
                
                {/* Lawyer Certification Section for Assets */}
                <div className="bg-gradient-to-r from-blue-50 to-indigo-50 p-4 rounded-xl border border-blue-200 mt-4">
                  <h4 className="font-semibold text-gray-800 mb-3 flex items-center">
                    <span className="mr-2">⚖️</span>
                    Asset Certification
                  </h4>
                  <div className="grid grid-cols-1 gap-3 sm:grid-cols-2">
                    <label className="flex items-center space-x-2 p-2 bg-white/50 rounded-lg">
                      <input
                        type="checkbox"
                        checked={newAsset.lawyerCertified}
                        onChange={(e) => setNewAsset({...newAsset, lawyerCertified: e.target.checked})}
                        className="rounded text-blue-600 focus:ring-blue-500"
                      />
                      <span className="text-sm font-medium sm:text-base">Lawyer Certified</span>
                    </label>
                    
                    {newAsset.lawyerCertified && (
                      <>
                        <input
                          type="text"
                          placeholder="Certifying Lawyer Name"
                          value={newAsset.lawyerName}
                          onChange={(e) => setNewAsset({...newAsset, lawyerName: e.target.value})}
                          className="p-3 border-2 border-blue-200 rounded-xl focus:ring-4 focus:ring-blue-300 focus:border-blue-400 bg-white/70 backdrop-blur-sm transition-all text-sm sm:text-base"
                        />
                        <input
                          type="date"
                          placeholder="Certification Date"
                          value={newAsset.certificationDate}
                          onChange={(e) => setNewAsset({...newAsset, certificationDate: e.target.value})}
                          className="p-3 border-2 border-blue-200 rounded-xl focus:ring-4 focus:ring-blue-300 focus:border-blue-400 bg-white/70 backdrop-blur-sm transition-all text-sm sm:text-base"
                        />
                      </>
                    )}
                  </div>
                </div>
                
                <div className="flex flex-col space-y-2 mt-3 sm:flex-row sm:space-y-0 sm:space-x-2">
                  <button
                    onClick={addAsset}
                    className="bg-gradient-to-r from-green-500 to-emerald-600 text-white px-4 py-2 rounded-xl hover:from-green-600 hover:to-emerald-700 transform hover:scale-105 transition-all shadow-lg font-semibold text-sm sm:text-base"
                  >
                    {editingIndex >= 0 ? 'Update' : 'Add'}
                  </button>
                  <button
                    onClick={() => {
                      setShowAssetForm(false);
                      setEditingIndex(-1);
                      setNewAsset({
                        name: '',
                        description: '',
                        value: '',
                        location: '',
                        beneficiary: '',
                        notes: '',
                        lawyerCertified: false,
                        certificationDate: '',
                        lawyerName: ''
                      });
                    }}
                    className="bg-gradient-to-r from-gray-400 to-gray-600 text-white px-4 py-2 rounded-xl hover:from-gray-500 hover:to-gray-700 transform hover:scale-105 transition-all shadow-lg font-semibold text-sm sm:text-base"
                  >
                    Cancel
                  </button>
                </div>
              </div>
            )}

            <div className="space-y-4">
              {assets[activeAssetCategory].map((asset, index) => (
                <div key={asset.id || index} className="bg-gradient-to-r from-white to-blue-50 p-4 rounded-xl border-2 border-blue-200 shadow-lg hover:shadow-xl transition-all">
                  <div className="flex flex-col space-y-3 sm:flex-row sm:justify-between sm:items-start sm:space-y-0">
                    <div className="flex-1">
                      <h3 className="font-bold text-lg text-gray-800 flex items-center">
                        {asset.name}
                        {asset.lawyerCertified && (
                          <span className="ml-2 bg-gradient-to-r from-green-400 to-blue-500 text-white text-xs px-2 py-1 rounded-full flex items-center">
                            ⚖️ Certified
                          </span>
                        )}
                      </h3>
                      <p className="text-gray-600 text-sm">{asset.description}</p>
                      <p className="text-green-600 font-semibold">${Number(asset.value).toLocaleString()}</p>
                      <p className="text-xs text-gray-500">📍 {asset.location}</p>
                      {asset.beneficiary && (
                        <p className="text-xs text-blue-600">→ {asset.beneficiary}</p>
                      )}
                      {asset.notes && (
                        <p className="text-xs text-gray-600 italic mt-1">"{asset.notes}"</p>
                      )}
                      {asset.lawyerCertified && (
                        <div className="mt-2 p-2 bg-green-50 rounded-lg border border-green-200">
                          <p className="text-xs text-green-700 font-medium">
                            ⚖️ Certified by: {asset.lawyerName || 'Not specified'}
                          </p>
                          <p className="text-xs text-green-600">
                            Date: {asset.certificationDate || 'Not specified'}
                          </p>
                        </div>
                      )}
                    </div>
                    <div className="flex space-x-2 justify-end">
                      <button
                        onClick={() => editAsset(activeAssetCategory, index)}
                        className="text-blue-500 hover:text-blue-700 p-2 hover:bg-blue-100 rounded-lg transition-all"
                      >
                        <Edit3 size={16} />
                      </button>
                      <button
                        onClick={() => deleteAsset(activeAssetCategory, index)}
                        className="text-red-500 hover:text-red-700 p-2 hover:bg-red-100 rounded-lg transition-all"
                      >
                        <Trash2 size={16} />
                      </button>
                    </div>
                  </div>
                </div>
              ))}
              
              {assets[activeAssetCategory].length === 0 && (
                <div className="text-center py-12">
                  <div className="text-6xl mb-4">{assetCategories[activeAssetCategory].icon}</div>
                  <h3 className="text-lg font-semibold text-gray-600 mb-2">No {assetCategories[activeAssetCategory].title} Yet</h3>
                  <p className="text-gray-500">Click "Add Item" to start adding your valuable possessions</p>
                </div>
              )}
            </div>
          </div>
        )}

        {/* Review Page */}
        {activeTab === 'review' && (
          <div className="bg-white/90 backdrop-blur-sm rounded-2xl p-4 shadow-xl border border-white/20 sm:p-8">
            <div className="flex items-center mb-6">
              <button
                onClick={() => setActiveTab('home')}
                className="mr-4 p-2 bg-gray-200 rounded-lg hover:bg-gray-300 transition-all"
              >
                <ArrowLeft size={20} />
              </button>
              <h2 className="text-xl font-bold text-gray-800 bg-gradient-to-r from-purple-500 to-pink-600 bg-clip-text text-transparent sm:text-3xl">Review & Create My Will</h2>
            </div>

            <div className="space-y-6">
              <div className="bg-gradient-to-r from-white to-purple-50 p-4 rounded-xl border-2 border-purple-200 shadow-lg">
                <h3 className="font-bold text-lg mb-2 text-gray-800 flex items-center">
                  👤 About Me
                  {isUnlocked ? (
                    <button
                      onClick={lockPersonalInfo}
                      className="ml-auto bg-gradient-to-r from-red-400 to-pink-500 text-white px-3 py-1 rounded-lg text-xs hover:from-red-500 hover:to-pink-600 transition-all flex items-center space-x-1"
                    >
                      <span>🔒</span>
                      <span>Lock</span>
                    </button>
                  ) : (
                    <span className="ml-auto bg-gradient-to-r from-red-400 to-pink-500 text-white px-3 py-1 rounded-lg text-xs flex items-center space-x-1">
                      <span>🔐</span>
                      <span>Locked</span>
                    </span>
                  )}
                </h3>
                {isUnlocked ? (
                  <div>
                    <p className="text-gray-700 text-sm">{personalInfo.fullName || 'Not specified'}</p>
                    <p className="text-gray-600 text-xs">{personalInfo.address && `${personalInfo.address}, ${personalInfo.city}, ${personalInfo.state} ${personalInfo.zipCode}`}</p>
                  </div>
                ) : (
                  <p className="text-gray-500 text-xs">Personal information locked - PIN required</p>
                )}
              </div>

              <div className="bg-gradient-to-r from-white to-blue-50 p-4 rounded-xl border-2 border-blue-200 shadow-lg">
                <h3 className="font-bold text-lg mb-2 text-gray-800 flex items-center">
                  ❤️ My Loved Ones ({beneficiaries.length})
                  <span className="ml-2 bg-blue-100 text-blue-800 text-xs px-2 py-1 rounded-full">
                    {beneficiaries.filter(b => b.lawyerCertified).length} Certified
                  </span>
                </h3>
                {beneficiaries.map((beneficiary, index) => (
                  <div key={index} className="mb-1">
                    <p className="text-gray-700 text-sm flex items-center">
                      {beneficiary.name} - {beneficiary.percentage}%
                      {beneficiary.lawyerCertified && (
                        <span className="ml-2 text-green-600 text-xs">⚖️</span>
                      )}
                    </p>
                  </div>
                ))}
                {totalPercentage !== 100 && beneficiaries.length > 0 && (
                  <p className="text-yellow-600 font-semibold text-sm">Warning: Total percentage is {totalPercentage}%, not 100%</p>
                )}
              </div>

              <div className="bg-gradient-to-r from-white to-green-50 p-4 rounded-xl border-2 border-green-200 shadow-lg">
                <h3 className="font-bold text-lg mb-2 text-gray-800 flex items-center">
                  💎 My Things
                  <span className="ml-2 bg-green-100 text-green-800 text-xs px-2 py-1 rounded-full">
                    {Object.values(assets).flat().filter(item => item.lawyerCertified).length} Certified
                  </span>
                </h3>
                {Object.entries(assets).map(([category, items]) => (
                  items.length > 0 && (
                    <div key={category} className="mb-2">
                      <p className="text-gray-700 text-sm flex items-center">
                        <span className="mr-2">{assetCategories[category].icon}</span>
                        {assetCategories[category].title}: {items.length} items
                        <span className="ml-2 text-green-600 text-xs">
                          ({items.filter(item => item.lawyerCertified).length} ⚖️)
                        </span>
                      </p>
                    </div>
                  )
                ))}
              </div>

              <button
                onClick={generateWill}
                className="w-full bg-gradient-to-r from-green-500 to-emerald-600 text-white px-6 py-4 rounded-xl hover:from-green-600 hover:to-emerald-700 transform hover:scale-105 transition-all shadow-lg flex items-center justify-center space-x-3 font-bold text-lg"
              >
                <Download size={24} />
                <span>Generate My Will Document</span>
              </button>

              <div className="bg-gradient-to-r from-yellow-100 to-orange-100 border-2 border-yellow-400 text-yellow-800 px-4 py-3 rounded-xl shadow-lg">
                <p className="font-bold text-sm">Important Legal Notice:</p>
                <p className="text-xs">This tool generates a basic will template for informational purposes only. Please consult with a qualified attorney to ensure your will meets all legal requirements in your jurisdiction and properly reflects your wishes.</p>
              </div>
            </div>
          </div>
        )}
      </div>
    </div>
  );
};

export default WillBeneficiaryApp;
