# DKP
import React, { useState, useEffect } from 'react';

import {

LayoutDashboard, Store, PackageSearch, MessageSquare,

User, Settings, Map, Filter, MapPin, TrendingUp,

AlertTriangle, CheckCircle, Truck, Search, Phone,

ChevronRight, Star, ArrowRight, ShieldCheck, Zap,

X, Send, WifiOff, Loader2, Bell, Clock, Package,

MoreVertical, RefreshCw, Sparkles

} from 'lucide-react';

// --- MOCK DATA ---

const MOCK_LISTINGS = [

{ id: 1, seller: 'Güven Et Entegre', type: 'Dana Karkas', date: 'Bugün (Sabah)', distance: 12, basePrice: 340, logistics: 5, pOpt: 345, rating: 4.8, verified: true, halal: true },

{ id: 2, seller: 'Özlem Mezbaha', type: 'Dana Karkas', date: 'Dün', distance: 45, basePrice: 335, logistics: 18, pOpt: 353, rating: 4.5, verified: true, halal: true },

{ id: 3, seller: 'Marmara Toptan Et', type: 'Kuzu Karkas', date: 'Bugün', distance: 8, basePrice: 410, logistics: 3, pOpt: 413, rating: 4.9, verified: true, halal: true },

{ id: 4, seller: 'Kardeşler Et', type: 'Dana Antrikot', date: '2 Gün Önce', distance: 25, basePrice: 650, logistics: 10, pOpt: 660, rating: 4.2, verified: false, halal: true },

];

const MOCK_BUYER_STOCKS = [

{ id: 1, name: 'Dana Karkas', current: 12, threshold: 50, unit: 'kg', status: 'critical', lastUpdated: '10 dk önce' },

{ id: 2, name: 'Kuzu Pirzola', current: 18, threshold: 20, unit: 'kg', status: 'warning', lastUpdated: '1 saat önce' },

{ id: 3, name: 'Dana Antrikot', current: 45, threshold: 15, unit: 'kg', status: 'good', lastUpdated: '3 saat önce' },

];

const MOCK_SELLER_RADAR = [

{ id: 1, client: 'Merkez Kasap', distance: 12, items: [{ name: 'Dana Karkas', current: 12, threshold: 50, status: 'critical' }] },

{ id: 2, client: 'Lezzet Lokantası', distance: 5, items: [{ name: 'Kuzu Pirzola', current: 18, threshold: 20, status: 'warning' }] },

{ id: 3, client: 'Yıldız Şarküteri', distance: 22, items: [{ name: 'Dana Kıyma', current: 5, threshold: 30, status: 'critical' }] },

];

const MOCK_MESSAGES = [

{ id: 1, sender: 'Güven Et Entegre', text: 'Merhaba, 50kg dana karkas siparişiniz yola çıktı.', time: '10:42', isMe: false },

{ id: 2, sender: 'Ben', text: 'Teşekkürler, tahmini ne zaman varır?', time: '10:45', isMe: true },

{ id: 3, sender: 'Güven Et Entegre', text: 'Trafiğe göre 45 dakika içinde kapınızda olur.', time: '10:47', isMe: false },

];

// --- MAIN APP COMPONENT ---

export default function DKPApp() {

const [role, setRole] = useState('buyer'); // 'buyer' | 'seller'

const [activeTab, setActiveTab] = useState('dashboard');

const [isOffline, setIsOffline] = useState(false);

const [isLoading, setIsLoading] = useState(false);

const [notifications, setNotifications] = useState([]);

// Simulate network loading state on tab change

const handleTabChange = (tab) => {

if (tab === activeTab) return;

setIsLoading(true);

setActiveTab(tab);

setTimeout(() => setIsLoading(false), 600);

};

const showToast = (msg) => {

const id = Date.now();

setNotifications(prev => [...prev, { id, msg }]);

setTimeout(() => {

  setNotifications(prev => prev.filter(n => n.id !== id));

}, 3000);

};

// --- SUB-COMPONENTS ---

const SkeletonLoader = () => (

<div className="space-y-4 p-6 animate-pulse">

  <div className="h-8 bg-slate-200 rounded w-1/4"></div>

  <div className="grid grid-cols-1 md:grid-cols-3 gap-6">

    {[1,2,3].map(i => (

      <div key={i} className="h-32 bg-slate-200 rounded-xl"></div>

    ))}

  </div>

  <div className="h-64 bg-slate-200 rounded-xl mt-6"></div>

</div>

);

const OfflineState = () => (

<div className="absolute inset-0 bg-white/80 backdrop-blur-sm z-50 flex flex-col items-center justify-center p-6 text-center animate-fade-in">

  <div className="bg-red-50 p-6 rounded-full mb-4">

    <WifiOff size={48} className="text-red-500" />

  </div>

  <h2 className="text-2xl font-bold text-slate-800 mb-2">Bağlantı Koptu</h2>

  <p className="text-slate-600 max-w-md mb-6">İnternet bağlantınız kesildi. Uygulama çevrimdışı modda çalışıyor, veriler önbellekten gösterilmektedir.</p>

  <button onClick={() => setIsOffline(false)} className="bg-slate-900 text-white px-6 py-3 rounded-xl font-medium flex items-center">

    <RefreshCw size={18} className="mr-2" /> Tekrar Bağlanmayı Dene

  </button>

</div>

);

// -- BUYER SCREENS --

const BuyerDashboard = () => (

<div className="p-4 md:p-6 space-y-6 animate-fade-in pb-24 md:pb-6">

  <div className="flex justify-between items-end">

    <div>

      <h1 className="text-2xl font-bold text-slate-800">Hoş Geldiniz, Merkez Kasap</h1>

      <p className="text-slate-500 text-sm mt-1">İstanbul, Avrupa Yakası piyasa özeti</p>

    </div>

  </div>



  <div className="grid grid-cols-2 md:grid-cols-4 gap-4">

    {[

      { label: 'Dana Karkas Ort.', price: '345₺', trend: 'up' },

      { label: 'Kuzu Karkas Ort.', price: '410₺', trend: 'down' },

      { label: 'Aktif Siparişler', price: '2', trend: 'neutral', icon: Truck },

      { label: 'Kritik Stok', price: '1', trend: 'danger', icon: AlertTriangle },

    ].map((stat, i) => (

      <div key={i} className="bg-white p-4 rounded-xl shadow-sm border border-slate-100">

        <div className="text-slate-500 text-sm mb-2 flex items-center justify-between">

          {stat.label}

          {stat.icon && <stat.icon size={16} className={stat.trend === 'danger' ? 'text-red-500' : 'text-blue-500'} />}

        </div>

        <div className="text-2xl font-bold text-slate-800 flex items-center">

          {stat.price}

          {stat.trend === 'up' && <TrendingUp size={16} className="text-red-500 ml-2" />}

          {stat.trend === 'down' && <TrendingUp size={16} className="text-emerald-500 ml-2 transform rotate-180" />}

        </div>

      </div>

    ))}

  </div>



  <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">

    <div className="lg:col-span-2 bg-white rounded-xl shadow-sm border border-slate-100 p-5">

      <div className="flex justify-between items-center mb-4">

        <h2 className="font-bold text-lg text-slate-800">Sizin İçin Fırsatlar (P_opt Sıralı)</h2>

        <button onClick={() => handleTabChange('marketplace')} className="text-emerald-600 text-sm font-medium hover:underline">Tümünü Gör</button>

      </div>

      <div className="space-y-4">

        {MOCK_LISTINGS.slice(0,2).map(listing => (

          <div key={listing.id} className="flex items-center justify-between p-4 border border-slate-100 rounded-lg hover:bg-slate-50 cursor-pointer transition-colors">

            <div className="flex items-center">

              <div className="bg-emerald-100 p-3 rounded-lg mr-4">

                <Store className="text-emerald-600" size={24} />

              </div>

              <div>

                <h4 className="font-bold text-slate-800">{listing.seller}</h4>

                <p className="text-sm text-slate-500">{listing.type} • {listing.distance} km</p>

              </div>

            </div>

            <div className="text-right">

              <div className="text-xl font-bold text-emerald-600">{listing.pOpt}₺<span className="text-sm text-slate-500 font-normal">/kg</span></div>

              <div className="text-xs text-slate-400 line-through">{listing.basePrice + listing.logistics}₺ (Lojistik Dahil)</div>

            </div>

          </div>

        ))}

      </div>

    </div>



    <div className="bg-red-50 rounded-xl border border-red-100 p-5">

      <div className="flex items-center mb-4">

        <AlertTriangle className="text-red-500 mr-2" size={20} />

        <h2 className="font-bold text-red-800">Acil Stok Uyarısı</h2>

      </div>

      <p className="text-sm text-red-700 mb-4">Dana Karkas stoğunuz kritik eşiğin altında! (Mevcut: 12kg)</p>

      <button onClick={() => handleTabChange('radar')} className="w-full bg-red-600 hover:bg-red-700 text-white py-2 rounded-lg font-medium transition-colors shadow-sm">

        Tedarikçiye Bildir

      </button>

    </div>

  </div>

</div>

);

const Marketplace = () => {

const [searchTerm, setSearchTerm] = useState('');

const [filterType, setFilterType] = useState('Tümü');



const filtered = MOCK_LISTINGS.filter(l => 

  (filterType === 'Tümü' || l.type.includes(filterType)) &&

  (l.seller.toLowerCase().includes(searchTerm.toLowerCase()) || l.type.toLowerCase().includes(searchTerm.toLowerCase()))

);



return (

  <div className="flex flex-col h-full animate-fade-in pb-24 md:pb-0">

    {/* Sticky Filter Bar */}

    <div className="bg-white border-b border-slate-200 p-4 sticky top-0 z-10 shadow-sm flex flex-col md:flex-row gap-3">

      <div className="flex-1 relative">

        <Search className="absolute left-3 top-1/2 transform -translate-y-1/2 text-slate-400" size={18} />

        <input 

          type="text" 

          placeholder="Satıcı veya et türü ara..." 

          className="w-full pl-10 pr-4 py-2.5 bg-slate-50 border border-slate-200 rounded-xl text-sm focus:ring-2 focus:ring-emerald-500 outline-none transition-all"

          value={searchTerm}

          onChange={(e) => setSearchTerm(e.target.value)}

        />

      </div>

      <div className="flex gap-2 overflow-x-auto pb-1 md:pb-0 hide-scrollbar">

        {['Tümü', 'Dana Karkas', 'Kuzu Karkas', 'Antrikot'].map(type => (

          <button 

            key={type}

            onClick={() => setFilterType(type)}

            className={`px-4 py-2 rounded-xl text-sm font-medium whitespace-nowrap transition-colors border ${

              filterType === type ? 'bg-emerald-600 text-white border-emerald-600' : 'bg-white text-slate-600 border-slate-200 hover:bg-slate-50'

            }`}

          >

            {type}

          </button>

        ))}

        <button className="px-4 py-2 rounded-xl text-sm font-medium bg-white text-slate-600 border border-slate-200 hover:bg-slate-50 flex items-center whitespace-nowrap">

          <Filter size={16} className="mr-2" /> Detaylı Filtre

        </button>

      </div>

    </div>



    <div className="flex flex-1 overflow-hidden">

      {/* Map Placeholder (Desktop) */}

      <div className="hidden lg:block w-1/3 bg-slate-200 relative border-r border-slate-200">

        <div className="absolute inset-0 bg-[url('https://api.mapbox.com/styles/v1/mapbox/light-v10/static/28.9784,41.0082,10,0/600x800?access_token=pk.eyJ1IjoiZXhhbXBsZSIsImEiOiJja2V4YW1wbGUifQ.example')] bg-cover bg-center opacity-50"></div>

        <div className="absolute inset-0 flex items-center justify-center">

          <div className="bg-white/90 backdrop-blur px-4 py-2 rounded-full font-medium text-slate-700 shadow-lg flex items-center">

            <MapPin size={18} className="text-emerald-600 mr-2" /> Harita Görünümü (Pro)

          </div>

        </div>

      </div>



      {/* List View */}

      <div className="flex-1 overflow-y-auto p-4 md:p-6 space-y-4 bg-slate-50">

        <div className="flex justify-between items-center mb-2">

          <span className="text-sm text-slate-500">{filtered.length} sonuç bulundu</span>

          <span className="text-sm font-medium text-slate-700 bg-emerald-100 px-3 py-1 rounded-full text-emerald-800 flex items-center">

            <Zap size={14} className="mr-1" /> P_opt Sıralamalı

          </span>

        </div>



        {filtered.length === 0 ? (

          <div className="bg-white rounded-2xl border border-dashed border-slate-300 p-12 text-center">

            <PackageSearch size={48} className="mx-auto text-slate-300 mb-4" />

            <h3 className="text-lg font-bold text-slate-700 mb-1">Sonuç Bulunamadı</h3>

            <p className="text-slate-500">Bu bölgede aradığınız kriterde et bulunamadı. Filtreleri genişletin.</p>

          </div>

        ) : (

          filtered.map(listing => (

            <div key={listing.id} className="bg-white rounded-2xl shadow-sm border border-slate-200 p-5 hover:shadow-md transition-all group">

              <div className="flex flex-col md:flex-row md:items-center justify-between gap-4">

                <div className="flex items-start gap-4">

                  <div className="w-16 h-16 bg-slate-100 rounded-xl flex items-center justify-center flex-shrink-0">

                     <Store className="text-slate-400" size={32} />

                  </div>

                  <div>

                    <div className="flex items-center gap-2 mb-1">

                      <h3 className="text-lg font-bold text-slate-800">{listing.seller}</h3>

                      {listing.verified && <ShieldCheck size={16} className="text-blue-500" title="Doğrulanmış İşletme" />}

                    </div>

                    <p className="text-sm font-medium text-slate-700 mb-1">{listing.type} • Kesim: {listing.date}</p>

                    <div className="flex items-center text-xs text-slate-500 gap-3">

                      <span className="flex items-center"><MapPin size={12} className="mr-1"/> {listing.distance} km</span>

                      <span className="flex items-center"><Star size={12} className="mr-1 text-amber-400"/> {listing.rating}</span>

                      {listing.halal && <span className="bg-emerald-50 text-emerald-700 px-2 py-0.5 rounded border border-emerald-100">Helal Kesim</span>}

                    </div>

                  </div>

                </div>

                

                <div className="bg-slate-50 p-4 rounded-xl border border-slate-100 flex flex-col md:items-end justify-center min-w-[200px]">

                  <div className="text-xs text-slate-500 mb-1">Optimum Tedarik Maliyeti</div>

                  <div className="text-2xl font-black text-emerald-600 flex items-baseline">

                    {listing.pOpt}₺ <span className="text-sm font-medium text-slate-500 ml-1">/kg</span>

                  </div>

                  <div className="text-xs text-slate-400 mt-1 line-through">Liste: {listing.basePrice}₺ + Loj: {listing.logistics}₺</div>

                </div>

              </div>



              <div className="mt-5 pt-4 border-t border-slate-100 flex gap-3">

                <button className="flex-1 py-3 px-4 bg-slate-900 hover:bg-slate-800 text-white rounded-xl text-sm font-semibold transition-colors flex items-center justify-center">

                  <Phone size={16} className="mr-2" /> Hemen Ara

                </button>

                <button 

                  onClick={() => { handleTabChange('messages'); showToast("Pazarlık odası açıldı."); }}

                  className="flex-1 py-3 px-4 bg-white border-2 border-slate-200 hover:border-emerald-500 hover:text-emerald-700 text-slate-700 rounded-xl text-sm font-semibold transition-colors flex items-center justify-center"

                >

                  <MessageSquare size={16} className="mr-2" /> Pazarlık Başlat

                </button>

              </div>

            </div>

          ))

        )}

      </div>

    </div>

  </div>

);

};

const BuyerRadar = () => (

<div className="p-4 md:p-6 space-y-6 animate-fade-in pb-24 md:pb-6">

  <div className="flex flex-col md:flex-row justify-between items-start md:items-center gap-4 mb-6">

    <div>

      <h1 className="text-2xl font-bold text-slate-800">Benim Stoklarım</h1>

      <p className="text-slate-500 mt-1">Stoklarınız bağlı toptancınız (Güven Et) ile anlık senkronize edilir.</p>

    </div>

    <button className="bg-emerald-100 text-emerald-700 px-4 py-2 rounded-lg text-sm font-semibold border border-emerald-200 flex items-center">

      <RefreshCw size={16} className="mr-2" /> Senkronize Edildi

    </button>

  </div>



  <div className="grid grid-cols-1 md:grid-cols-3 gap-6">

    {MOCK_BUYER_STOCKS.map(stock => (

      <div key={stock.id} className={`bg-white rounded-2xl shadow-sm border-l-4 p-6 transition-all hover:shadow-md ${

        stock.status === 'critical' ? 'border-l-red-500' : 

        stock.status === 'warning' ? 'border-l-amber-500' : 'border-l-emerald-500'

      }`}>

         <div className="flex justify-between items-start mb-4">

            <h3 className="text-lg font-bold text-slate-800">{stock.name}</h3>

            {stock.status === 'critical' && <AlertTriangle className="text-red-500 animate-pulse" size={24} />}

            {stock.status === 'warning' && <AlertTriangle className="text-amber-500" size={24} />}

            {stock.status === 'good' && <CheckCircle className="text-emerald-500" size={24} />}

          </div>

          

          <div className="mb-6">

            <div className="flex justify-between text-sm mb-2">

              <span className="text-slate-500">Mevcut:</span>

              <span className={font-bold text-xl ${stock.status === 'critical' ? 'text-red-600' : 'text-slate-800'}}>

                {stock.current} {stock.unit}

              </span>

            </div>

            <div className="w-full bg-slate-100 rounded-full h-3 overflow-hidden">

              <div 

                className={`h-full rounded-full transition-all duration-1000 ${

                  stock.status === 'critical' ? 'bg-red-500' : stock.status === 'warning' ? 'bg-amber-400' : 'bg-emerald-500'

                }`} 

                style={{ width: ${Math.min(100, (stock.current / (stock.threshold * 2)) * 100)}% }}

              ></div>

            </div>

            <p className="text-xs text-slate-400 mt-2">Kritik Eşik: {stock.threshold} {stock.unit} • {stock.lastUpdated}</p>

          </div>



          {stock.status !== 'good' ? (

             <button 

              onClick={() => showToast(${stock.name} için acil tedarik isteği gönderildi!)}

              className={`w-full py-3 px-4 rounded-xl font-bold flex items-center justify-center transition-transform hover:scale-105 active:scale-95 ${

                stock.status === 'critical' 

                  ? 'bg-red-600 text-white shadow-lg shadow-red-200' 

                  : 'bg-amber-500 text-white shadow-lg shadow-amber-200'

              }`}

            >

              <Truck size={18} className="mr-2" />

              Sistemden Tedarik İste

            </button>

          ) : (

            <div className="w-full py-3 px-4 rounded-xl bg-slate-50 text-slate-400 text-sm text-center font-medium border border-slate-100">

              Stok Seviyesi Yeterli

            </div>

          )}

      </div>

    ))}

  </div>

</div>

);

// -- SELLER SCREENS --

const SellerRadar = () => (

<div className="p-4 md:p-6 space-y-6 animate-fade-in pb-24 md:pb-6">

  <div className="flex flex-col md:flex-row justify-between items-start md:items-center gap-4 mb-6">

    <div>

      <h1 className="text-2xl font-bold text-slate-800">Müşteri Stok Radarı</h1>

      <p className="text-slate-500 mt-1">Ağınıza bağlı 12 müşterinin anlık eksikleri.</p>

    </div>

    <button onClick={() => { handleTabChange('messages'); showToast("Yapay Zeka Stratejisi hazırlanıyor..."); }} className="bg-gradient-to-r from-indigo-600 to-purple-600 hover:from-indigo-700 hover:to-purple-700 text-white px-5 py-2.5 rounded-xl text-sm font-bold shadow-lg shadow-purple-200 flex items-center transition-transform hover:scale-105">

      <Sparkles size={18} className="mr-2" /> AI Günlük Strateji Al

    </button>

  </div>



  <div className="grid grid-cols-3 gap-4 mb-8">

    <div className="bg-red-50 border border-red-100 rounded-xl p-4 text-center">

      <div className="text-3xl font-black text-red-600 mb-1">3</div>

      <div className="text-xs font-bold text-red-800 uppercase tracking-wider">Acil Müşteri</div>

    </div>

    <div className="bg-amber-50 border border-amber-100 rounded-xl p-4 text-center">

      <div className="text-3xl font-black text-amber-600 mb-1">5</div>

      <div className="text-xs font-bold text-amber-800 uppercase tracking-wider">Uyarıda</div>

    </div>

    <div className="bg-emerald-50 border border-emerald-100 rounded-xl p-4 text-center">

      <div className="text-3xl font-black text-emerald-600 mb-1">4</div>

      <div className="text-xs font-bold text-emerald-800 uppercase tracking-wider">Güvende</div>

    </div>

  </div>



  <div className="bg-white rounded-2xl shadow-sm border border-slate-200 overflow-hidden">

    <div className="bg-slate-50 p-4 border-b border-slate-200 flex items-center">

      <AlertTriangle className="text-slate-500 mr-2" size={20} />

      <h2 className="font-bold text-slate-700">Aksiyon Bekleyenler</h2>

    </div>

    <div className="divide-y divide-slate-100">

      {MOCK_SELLER_RADAR.map(client => (

        <div key={client.id} className="p-5 hover:bg-slate-50 transition-colors flex flex-col md:flex-row justify-between items-start md:items-center gap-4">

          <div className="flex-1">

            <div className="flex items-center gap-3 mb-2">

              <h3 className="text-lg font-bold text-slate-800">{client.client}</h3>

              <span className="text-xs font-medium bg-slate-100 text-slate-600 px-2 py-1 rounded-md flex items-center">

                <MapPin size={12} className="mr-1"/> {client.distance} km

              </span>

            </div>

            {client.items.map((item, idx) => (

               <div key={idx} className="flex items-center text-sm">

                  <Package size={14} className="text-slate-400 mr-2" />

                  <span className="text-slate-600 mr-2">{item.name}:</span>

                  <span className={font-bold mr-2 ${item.status === 'critical' ? 'text-red-600' : 'text-amber-600'}}>

                    {item.current} kg kaldı

                  </span>

                  <span className="text-slate-400 text-xs">(Sınır: {item.threshold} kg)</span>

               </div>

            ))}

          </div>

          <div className="flex w-full md:w-auto gap-3 mt-4 md:mt-0">

            <button className="flex-1 md:flex-none px-4 py-2 border border-slate-300 text-slate-700 rounded-xl font-medium text-sm hover:bg-slate-100 transition-colors flex items-center justify-center">

              <Phone size={16} className="mr-2" /> Ara

            </button>

            <button 

              onClick={() => showToast(${client.client} için sipariş hazırlığı başlatıldı.)}

              className={`flex-1 md:flex-none px-5 py-2 text-white rounded-xl font-medium text-sm shadow-md flex items-center justify-center transition-transform active:scale-95 ${

                client.items[0].status === 'critical' ? 'bg-blue-600 hover:bg-blue-700 shadow-blue-200' : 'bg-slate-800 hover:bg-slate-900 shadow-slate-200'

              }`}

            >

              <Truck size={16} className="mr-2" /> 

              {client.items[0].status === 'critical' ? 'Siparişi Yola Çıkar' : 'Hazırlığa Başla'}

            </button>

          </div>

        </div>

      ))}

    </div>

  </div>

</div>

);

// -- SHARED SCREENS --

const Messages = () => {

const [msgText, setMsgText] = useState('');



return (

  <div className="flex h-[calc(100vh-80px)] md:h-full pb-16 md:pb-0 bg-white md:m-6 md:rounded-2xl md:shadow-sm md:border md:border-slate-200 overflow-hidden animate-fade-in">

    {/* Contacts Sidebar */}

    <div className="w-1/3 border-r border-slate-100 flex flex-col hidden md:flex">

      <div className="p-4 border-b border-slate-100 bg-slate-50">

        <h2 className="font-bold text-slate-800">Mesajlar</h2>

      </div>

      <div className="overflow-y-auto flex-1">

        <div className="p-4 border-b border-slate-50 bg-emerald-50/50 cursor-pointer flex items-center justify-between">

          <div>

            <h4 className="font-bold text-slate-800">{role === 'buyer' ? 'Güven Et Entegre' : 'Merkez Kasap'}</h4>

            <p className="text-sm text-slate-500 truncate mt-1">Trafiğe göre 45 dakika içinde...</p>

          </div>

          <div className="w-2 h-2 bg-emerald-500 rounded-full"></div>

        </div>

        <div className="p-4 border-b border-slate-50 cursor-pointer hover:bg-slate-50 opacity-60">

          <h4 className="font-bold text-slate-800">{role === 'buyer' ? 'Özlem Mezbaha' : 'Yıldız Şarküteri'}</h4>

          <p className="text-sm text-slate-500 truncate mt-1">Fiyat teklifini inceledim...</p>

        </div>

      </div>

    </div>



    {/* Chat Area */}

    <div className="flex-1 flex flex-col bg-[#f0f2f5] relative">

       <div className="absolute inset-0 bg-[url('https://www.transparenttextures.com/patterns/cubes.png')] opacity-5 pointer-events-none"></div>

       

       {/* Chat Header */}

       <div className="bg-white p-4 border-b border-slate-200 flex justify-between items-center z-10 shadow-sm">

         <div className="flex items-center">

           <div className="w-10 h-10 bg-slate-200 rounded-full flex items-center justify-center mr-3">

             <Store className="text-slate-500" size={20} />

           </div>

           <div>

             <h3 className="font-bold text-slate-800">{role === 'buyer' ? 'Güven Et Entegre' : 'Merkez Kasap'}</h3>

             <span className="text-xs text-emerald-600 font-medium">Çevrimiçi</span>

           </div>

         </div>

         <MoreVertical className="text-slate-400 cursor-pointer" />

       </div>



       {/* Chat History */}

       <div className="flex-1 overflow-y-auto p-4 space-y-4 z-10">

         {MOCK_MESSAGES.map(msg => (

           <div key={msg.id} className={flex ${msg.isMe ? 'justify-end' : 'justify-start'}}>

             <div className={`max-w-[75%] rounded-2xl p-3 shadow-sm text-sm relative ${

               msg.isMe ? 'bg-emerald-100 text-slate-800 rounded-tr-none' : 'bg-white text-slate-800 border border-slate-100 rounded-tl-none'

             }`}>

               <p className="leading-relaxed">{msg.text}</p>

               <span className="text-[10px] text-slate-400 block text-right mt-1">{msg.time}</span>

             </div>

           </div>

         ))}

       </div>



       {/* Chat Input */}

       <div className="bg-white p-4 z-10 border-t border-slate-200">

         {role === 'buyer' && (

            <div className="mb-3 flex justify-end">

              <button onClick={() => setMsgText("Acil 50kg Dana Karkas tedarik edebilir misiniz? Stoklarımız kritik seviyede.")} className="bg-purple-50 hover:bg-purple-100 text-purple-700 border border-purple-200 px-3 py-1.5 rounded-lg text-xs font-bold flex items-center transition-colors">

                <Sparkles size={14} className="mr-1" /> AI: Acil Sipariş Mesajı Yaz

              </button>

            </div>

         )}

         <div className="flex items-center gap-2">

           <input 

             type="text" 

             placeholder="Mesajınızı yazın..." 

             className="flex-1 bg-slate-100 border-none rounded-full px-4 py-3 text-sm focus:ring-2 focus:ring-emerald-500 outline-none"

             value={msgText}

             onChange={(e) => setMsgText(e.target.value)}

             onKeyDown={(e) => e.key === 'Enter' && setMsgText('')}

           />

           <button 

             onClick={() => { if(msgText) { setMsgText(''); showToast("Mesaj gönderildi"); } }}

             className={p-3 rounded-full flex items-center justify-center transition-colors ${msgText ? 'bg-emerald-600 text-white' : 'bg-slate-200 text-slate-400'}}

           >

             <Send size={18} />

           </button>

         </div>

       </div>

    </div>

  </div>

);

};

const OrdersPlaceholder = () => (

<div className="flex flex-col items-center justify-center h-full p-6 animate-fade-in text-center">

  <div className="bg-slate-100 p-6 rounded-full mb-6">

    <Package className="text-slate-300" size={64} />

  </div>

  <h2 className="text-2xl font-bold text-slate-700 mb-2">Henüz Sipariş Yok</h2>

  <p className="text-slate-500 max-w-sm">Aktif veya geçmiş siparişiniz bulunmamaktadır. Pazar yerinden yeni ürünler keşfedin.</p>

</div>

);

const ProfilePlaceholder = () => (

<div className="p-4 md:p-6 max-w-2xl mx-auto animate-fade-in pb-24 md:pb-6">

  <div className="bg-white rounded-2xl shadow-sm border border-slate-200 overflow-hidden">

    <div className="bg-slate-900 h-32 relative">

       <div className="absolute -bottom-10 left-6 w-20 h-20 bg-white rounded-2xl border-4 border-white shadow-lg flex items-center justify-center">

         <Store size={32} className="text-emerald-600" />

       </div>

    </div>

    <div className="pt-14 p-6">

      <div className="flex justify-between items-start mb-6">

        <div>

          <h2 className="text-2xl font-bold text-slate-800">{role === 'buyer' ? 'Merkez Kasap' : 'Güven Et Entegre'}</h2>

          <p className="text-slate-500 text-sm mt-1">Vergi No: 1234567890 • İstanbul</p>

        </div>

        <span className="bg-blue-50 text-blue-700 px-3 py-1 rounded-full text-sm font-bold border border-blue-100 flex items-center">

          <ShieldCheck size={16} className="mr-1" /> Doğrulanmış

        </span>

      </div>



      <div className="space-y-4">

        <div className="p-4 bg-slate-50 rounded-xl border border-slate-100">

           <h3 className="font-bold text-slate-700 text-sm mb-1">Hesap Türü</h3>

           <p className="text-slate-600">{role === 'buyer' ? 'Alıcı (Perakende/Tüketici)' : 'Satıcı (Toptan/Üretici)'}</p>

        </div>

        <div className="p-4 bg-slate-50 rounded-xl border border-slate-100">

           <h3 className="font-bold text-slate-700 text-sm mb-1">Bildirim Tercihleri</h3>

           <p className="text-slate-600">SMS ve Push Bildirimleri (Açık)</p>

        </div>

      </div>



      <button className="w-full mt-6 py-3 border-2 border-slate-200 text-slate-700 rounded-xl font-bold hover:bg-slate-50 transition-colors">

        Profili Düzenle

      </button>

    </div>

  </div>

</div>

);

// --- NAVIGATION CONFIG ---

const buyerNav = [

{ id: 'dashboard', label: 'Ana Sayfa', icon: LayoutDashboard },

{ id: 'marketplace', label: 'Pazar Yeri', icon: PackageSearch },

{ id: 'radar', label: 'Stok Radarı', icon: Activity },

{ id: 'orders', label: 'Siparişler', icon: Clock },

{ id: 'messages', label: 'Mesajlar', icon: MessageSquare },

{ id: 'profile', label: 'Profil', icon: User },

];

const sellerNav = [

{ id: 'dashboard', label: 'Stok Radarı', icon: Activity },

{ id: 'marketplace', label: 'İlanlarım', icon: LayoutDashboard }, // using marketplace ID to reuse logic but visually it's listings

{ id: 'orders', label: 'Talepler', icon: Clock },

{ id: 'messages', label: 'Mesajlar', icon: MessageSquare },

{ id: 'profile', label: 'Profil', icon: User },

];

const currentNav = role === 'buyer' ? buyerNav : sellerNav;

// Render main content based on state

const renderContent = () => {

if (isLoading) return <SkeletonLoader />;



if (role === 'buyer') {

  switch(activeTab) {

    case 'dashboard': return <BuyerDashboard />;

    case 'marketplace': return <Marketplace />;

    case 'radar': return <BuyerRadar />;

    case 'messages': return <Messages />;

    case 'orders': return <OrdersPlaceholder />;

    case 'profile': return <ProfilePlaceholder />;

    default: return <BuyerDashboard />;

  }

} else {

  switch(activeTab) {

    case 'dashboard': return <SellerRadar />;

    case 'messages': return <Messages />;

    case 'profile': return <ProfilePlaceholder />;

    default: return <div className="p-6 text-center text-slate-500 mt-20">Bu modül (Satıcı - {activeTab}) yakında eklenecek.</div>;

  }

}

};

return (

<div className="flex h-screen bg-[#f8fafc] overflow-hidden font-sans text-slate-800">

  

  {/* --- DESKTOP SIDEBAR --- */}

  <aside className="w-64 bg-slate-900 text-slate-300 hidden md:flex flex-col shadow-2xl z-20 transition-all">

    <div className="p-6 border-b border-slate-800 flex items-center gap-3">

      <div className="bg-emerald-500 p-2 rounded-lg">

         <Store className="text-white" size={24} />

      </div>

      <div>

        <h1 className="text-xl font-black text-white tracking-tight">DKP<span className="text-emerald-400">.</span></h1>

        <span className="text-[10px] uppercase tracking-widest text-slate-500 font-bold">Karkas Pazarı</span>

      </div>

    </div>



    <nav className="flex-1 p-4 flex flex-col gap-2 overflow-y-auto">

      <div className="text-xs font-bold text-slate-500 uppercase mb-2 pl-2 mt-4">Menü</div>

      {currentNav.map(item => {

        const Icon = item.icon;

        const isActive = activeTab === item.id;

        return (

          <button 

            key={item.id}

            onClick={() => handleTabChange(item.id)}

            className={`flex items-center gap-3 px-4 py-3 rounded-xl transition-all font-medium text-sm ${

              isActive ? 'bg-emerald-600 text-white shadow-lg shadow-emerald-900/50' : 'hover:bg-slate-800 hover:text-white'

            }`}

          >

            <Icon size={18} className={isActive ? 'text-emerald-100' : 'text-slate-400'} />

            {item.label}

          </button>

        )

      })}

    </nav>



    <div className="p-4 border-t border-slate-800">

      <div className="bg-slate-800 rounded-xl p-4 flex items-center justify-between">

        <div className="flex flex-col">

          <span className="text-xs font-bold text-slate-400 mb-1">Görünüm Testi</span>

          <select 

            value={role} 

            onChange={(e) => { setRole(e.target.value); setActiveTab('dashboard'); }}

            className="bg-transparent text-white text-sm outline-none font-medium border-b border-slate-600 pb-1 cursor-pointer"

          >

            <option value="buyer" className="text-slate-800">Alıcı (Kasap)</option>

            <option value="seller" className="text-slate-800">Satıcı (Toptancı)</option>

          </select>

        </div>

      </div>

    </div>

  </aside>



  {/* --- MAIN CONTENT AREA --- */}

  <div className="flex-1 flex flex-col h-full relative overflow-hidden w-full">

    

    {/* Top Header */}

    <header className="bg-white border-b border-slate-200 h-16 flex items-center justify-between px-4 md:px-6 shrink-0 z-10 shadow-sm relative">

       {/* Mobile Brand */}

       <div className="md:hidden flex items-center gap-2">

          <div className="bg-emerald-500 p-1.5 rounded-lg">

            <Store className="text-white" size={18} />

          </div>

          <h1 className="text-lg font-black text-slate-900 tracking-tight">DKP<span className="text-emerald-500">.</span></h1>

       </div>



       {/* Breadcrumb / Title (Desktop) */}

       <div className="hidden md:flex items-center text-sm font-medium text-slate-500">

         <span className="capitalize">{role === 'buyer' ? 'Alıcı Paneli' : 'Satıcı Paneli'}</span>

         <ChevronRight size={14} className="mx-2 opacity-50" />

         <span className="text-slate-800">{currentNav.find(n => n.id === activeTab)?.label}</span>

       </div>



       {/* Right Actions */}

       <div className="flex items-center gap-3">

         <button onClick={() => setIsOffline(!isOffline)} className={p-2 rounded-full transition-colors ${isOffline ? 'bg-red-100 text-red-600' : 'bg-slate-100 text-slate-600 hover:bg-slate-200'}} title="Ağ Durumunu Simüle Et">

           {isOffline ? <WifiOff size={18} /> : <Zap size={18} />}

         </button>

         <div className="relative cursor-pointer">

           <div className="bg-slate-100 p-2 rounded-full hover:bg-slate-200 transition-colors">

              <Bell size={18} className="text-slate-600" />

           </div>

           <span className="absolute top-0 right-0 w-2.5 h-2.5 bg-red-500 rounded-full border-2 border-white"></span>

         </div>

         <div className="w-8 h-8 bg-emerald-600 rounded-full flex items-center justify-center text-white font-bold text-sm shadow-md cursor-pointer ml-2">

           {role === 'buyer' ? 'M' : 'G'}

         </div>

       </div>

    </header>



    {/* Content Scrollable Area */}

    <main className="flex-1 overflow-y-auto relative w-full hide-scrollbar">

      {isOffline ? <OfflineState /> : renderContent()}

    </main>



    {/* Floating Toasts */}

    <div className="absolute top-20 right-4 z-50 flex flex-col gap-2 pointer-events-none">

      {notifications.map(n => (

        <div key={n.id} className="bg-slate-800 text-white px-5 py-3 rounded-xl shadow-2xl flex items-center gap-3 animate-fade-in pointer-events-auto">

          <CheckCircle size={18} className="text-emerald-400" />

          <span className="text-sm font-medium">{n.msg}</span>

        </div>

      ))}

    </div>



  </div>



  {/* --- MOBILE BOTTOM NAV --- */}

  <nav className="md:hidden fixed bottom-0 left-0 w-full bg-white border-t border-slate-200 px-2 py-2 flex justify-around items-center z-30 shadow-[0_-4px_20px_-10px_rgba(0,0,0,0.1)] pb-safe">

    {currentNav.slice(0, 5).map(item => {

      const Icon = item.icon;

      const isActive = activeTab === item.id;

      return (

        <button 

          key={item.id}

          onClick={() => handleTabChange(item.id)}

          className={`flex flex-col items-center justify-center w-16 h-14 rounded-xl transition-all ${

            isActive ? 'text-emerald-600' : 'text-slate-400 hover:text-slate-600'

          }`}

        >

          <div className={p-1.5 rounded-lg mb-1 ${isActive ? 'bg-emerald-50' : ''}}>

            <Icon size={20} className={isActive ? 'fill-emerald-100 stroke-2' : 'stroke-[1.5]'} />

          </div>

          <span className={text-[10px] font-semibold tracking-wide ${isActive ? 'opacity-100' : 'opacity-70'}}>

            {item.label}

          </span>

        </button>

      )

    })}

  </nav>



  {/* Tailwind Custom CSS classes injected via style tag for animations */}

  <style dangerouslySetInnerHTML={{__html: `

    @keyframes fade-in { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }

    .animate-fade-in { animation: fade-in 0.3s ease-out forwards; }

    .hide-scrollbar::-webkit-scrollbar { display: none; }

    .hide-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }

    .pb-safe { padding-bottom: env(safe-area-inset-bottom, 16px); }

  `}} />

</div>

);

}

// Temporary icon stub for Activity since it wasn't imported from lucide

function Activity(props) {

return <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" {...props}><polyline points="22 12 18 12 15 21 9 3 6 12 2 12"></polyline></svg>

}
