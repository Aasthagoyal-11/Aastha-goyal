//Member 1 – Signup & OTP Pages // --- Member 1 START --- // File: Signup.jsx import { useState } from "react"; import { useNavigate } from "react-router-dom"; import { Input } from "@/components/ui/input"; import { Button } from "@/components/ui/button"; import { toast } from "@/hooks/use-toast";

const Signup = () => { const navigate = useNavigate(); const [name, setName] = useState(""); const [aadhaar, setAadhaar] = useState(""); const [pan, setPan] = useState(""); const [mobile, setMobile] = useState("");

const handleSignup = () => { if (!name || !aadhaar || !pan || !mobile) { toast({ title: "Error", description: "Please fill all fields" }); return; } localStorage.setItem("userData", JSON.stringify({ name, aadhaar, pan, mobile })); localStorage.setItem("isAuthenticated", "false"); navigate("/verify-otp"); };

return (

Signup
<Input placeholder="Name" value={name} onChange={(e) => setName(e.target.value)} className="mb-2" /> <Input placeholder="Aadhaar" value={aadhaar} onChange={(e) => setAadhaar(e.target.value)} className="mb-2" /> <Input placeholder="PAN" value={pan} onChange={(e) => setPan(e.target.value)} className="mb-2" /> <Input placeholder="Mobile" value={mobile} onChange={(e) => setMobile(e.target.value)} className="mb-4" /> Signup
); };
export default Signup;

// File: OTPVerification.jsx import { useState } from "react"; import { useNavigate } from "react-router-dom"; import { Input } from "@/components/ui/input"; import { Button } from "@/components/ui/button"; import { toast } from "@/hooks/use-toast";

const OTPVerification = () => { const navigate = useNavigate(); const [otp, setOtp] = useState("");

const handleVerify = () => { if (otp === "123456") { localStorage.setItem("isAuthenticated", "true"); navigate("/dashboard"); toast({ title: "Success", description: "OTP Verified" }); } else { toast({ title: "Error", description: "Incorrect OTP" }); } };

return (

Enter OTP
<Input placeholder="Enter OTP" value={otp} onChange={(e) => setOtp(e.target.value)} className="mb-4" /> Verify OTP
); };
//export default OTPVerification; // --- Member 1 END --- //Member 2 – Dashboard Header // --- Member 2 START --- // File: DashboardHeader.jsx import { useState, useEffect } from "react"; import { useNavigate } from "react-router-dom"; import { ThemeToggle } from "@/components/ThemeToggle"; import { Button } from "@/components/ui/button"; import { User, LogOut, TrendingUp } from "lucide-react"; import { toast } from "@/hooks/use-toast";

const DashboardHeader = () => { const navigate = useNavigate(); const [userData, setUserData] = useState({ name: "User" });

useEffect(() => { const stored = JSON.parse(localStorage.getItem("userData") || "{}"); if (!stored || localStorage.getItem("isAuthenticated") !== "true") { navigate("/signup"); } else setUserData(stored); }, [navigate]);

const handleLogout = () => { localStorage.removeItem("isAuthenticated"); localStorage.removeItem("userData"); navigate("/signup"); toast({ title: "Logged out", description: "You have logged out" }); };

return (

StockPro
{userData.name}
); };
export default DashboardHeader; // --- Member 2 END ---

//Member 3 – Market Indices Component // --- Member 3 START --- // File: MarketIndices.jsx import { MarketChart } from "@/components/MarketChart"; import { BarChart3 } from "lucide-react";

// Mock data for indices const marketIndices = [ { name: "NIFTY 50", value: 21850.45, change: 245.3, changePercent: 1.13, trend: "up" }, { name: "SENSEX", value: 72456.78, change: -89.45, changePercent: -0.12, trend: "down" }, { name: "MIDCAP", value: 12345.67, change: 156.89, changePercent: 1.29, trend: "up" }, { name: "BANK NIFTY", value: 48567.23, change: 432.56, changePercent: 0.9, trend: "up" } ];

const MarketIndices = () => { return (

{marketIndices.map((index) => ( ))}
); };
export default MarketIndices; // --- Member 3 END ---

//Member 4 – Stock Search Component // --- Member 4 START --- // File: StockSearch.jsx import { useState } from "react"; import { Card, CardContent, CardHeader, CardTitle, Badge } from "@/components/ui/card"; import { Input } from "@/components/ui/input"; import { Button } from "@/components/ui/button"; import { useNavigate } from "react-router-dom"; import { toast } from "@/hooks/use-toast";

const StockSearch = () => { const navigate = useNavigate(); const [searchQuery, setSearchQuery] = useState(""); const [searchResults, setSearchResults] = useState([]); const [isSearching, setIsSearching] = useState(false);

const handleSearch = async () => { if (!searchQuery.trim()) return; setIsSearching(true); await new Promise(r => setTimeout(r, 1000)); const mockResults = [ { symbol: searchQuery.toUpperCase(), name: ${searchQuery} Ltd., price: Math.floor(Math.random() * 5000) + 100, change: (Math.random() - 0.5) * 100, changePercent: (Math.random() - 0.5) * 10, volume: Math.floor(Math.random() * 1000000) + 10000 } ]; setSearchResults(mockResults); setIsSearching(false); };

const handleBuySell = (action, stock) => { const portfolio = JSON.parse(localStorage.getItem("portfolio") || "[]"); portfolio.push({ id: Date.now(), symbol: stock.symbol, name: stock.name, action, price: stock.price, quantity: 1, timestamp: new Date().toISOString() }); localStorage.setItem("portfolio", JSON.stringify(portfolio)); toast({ title: ${action.toUpperCase()} Order Placed, description: ${action} 1 share of ${stock.symbol} at ₹${stock.price} }); };

return ( Stock Search

<Input placeholder="Enter stock symbol" value={searchQuery} onChange={(e) => setSearchQuery(e.target.value)} /> <Button onClick={handleSearch} disabled={!searchQuery || isSearching}> {isSearching ? "Searching..." : "Search"}
{searchResults.map((stock, i) => ( <Card key={i} className="mb-2 cursor-pointer" onClick={() => navigate(/stock/${stock.symbol.toLowerCase()})}>
{stock.symbol}

{stock.name}

₹{stock.price.toFixed(2)} ({stock.change.toFixed(2)})

<Button size="sm" onClick={(e)=>{e.stopPropagation(); handleBuySell('buy', stock)}}>Buy <Button size="sm" variant="destructive" onClick={(e)=>{e.stopPropagation(); handleBuySell('sell', stock)}}>Sell
))} ); };
export default StockSearch; //Member 5 – Stock Detail Page // --- Member 5 START --- // File: StockDetail.jsx import { useState, useEffect } from "react"; import { useParams, useNavigate } from "react-router-dom"; import { Card, CardContent, CardHeader, CardTitle, Badge } from "@/components/ui/card"; import { Button } from "@/components/ui/button"; import { toast } from "@/hooks/use-toast"; import { TrendingUp, TrendingDown, BarChart3, Activity } from "lucide-react"; import { AreaChart, Area, XAxis, YAxis, CartesianGrid, Tooltip, ResponsiveContainer } from "recharts";

const StockDetail = () => { const { symbol } = useParams(); const navigate = useNavigate(); const [stockData, setStockData] = useState(null); const [chartData, setChartData] = useState([]);

useEffect(() => { const isAuthenticated = localStorage.getItem("isAuthenticated"); if (!isAuthenticated) navigate("/signup");

if (symbol) {
  const mockStock = {
    symbol: symbol.toUpperCase(),
    name: ${symbol} Ltd.,
    price: Math.floor(Math.random() * 5000) + 100,
    change: (Math.random() - 0.5) * 200,
    changePercent: (Math.random() - 0.5) * 10,
    volume: Math.floor(Math.random() * 10000000) + 100000,
  };
  setStockData(mockStock);

  // Fake chart data
  const data = [];
  const basePrice = mockStock.price - mockStock.change;
  for (let i = 0; i < 50; i++) {
    const time = ${i}:00;
    const price = Math.max(basePrice + (Math.random() - 0.5) * (mockStock.price * 0.02), basePrice * 0.8);
    data.push({ time, price });
  }
  setChartData(data);
}
}, [symbol, navigate]);

const handleBuySell = (action) => { if (!stockData) return; const portfolio = JSON.parse(localStorage.getItem("portfolio") || "[]"); portfolio.push({ id: Date.now(), symbol: stockData.symbol, name: stockData.name, action, price: stockData.price, quantity: 1 }); localStorage.setItem("portfolio", JSON.stringify(portfolio)); toast({ title: ${action.toUpperCase()} Order, description: ${action} 1 share of ${stockData.symbol} at ₹${stockData.price} }); };

if (!stockData) return

;
return (

{stockData.symbol} - {stockData.name}
₹{stockData.price} ({stockData.change.toFixed(2)}, {stockData.changePercent.toFixed(2)}%)

{stockData.volume} Vol
<Button onClick={() => handleBuySell("buy")}>Buy <Button variant="destructive" onClick={() => handleBuySell("sell")}>Sell
  {/* Chart */}
  <Card className="mt-6">
    <CardHeader><CardTitle>Price Trend</CardTitle></CardHeader>
    <CardContent>
      <ResponsiveContainer width="100%" height={300}>
        <AreaChart data={chartData}>
          <CartesianGrid strokeDasharray="3 3" />
          <XAxis dataKey="time" />
          <YAxis />
          <Tooltip />
          <Area type="monotone" dataKey="price" stroke="#2563eb" fill="#93c5fd" />
        </AreaChart>
      </ResponsiveContainer>
    </CardContent>
  </Card>
</div>
); };

export default StockDetail; // --- Member 5 END ---

//Member 6 – Portfolio Page // --- Member 6 START --- // File: Portfolio.jsx import { useState, useEffect } from "react"; import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";

const Portfolio = () => { const [portfolio, setPortfolio] = useState([]);

useEffect(() => { const stored = JSON.parse(localStorage.getItem("portfolio") || "[]"); setPortfolio(stored); }, []);

return (

Your Portfolio {portfolio.length === 0 ? (
No transactions yet

) : (
{portfolio.map((item) => (
{item.action.toUpperCase()} {item.quantity} {item.symbol} @ ₹{item.price}
))}
)}
); };
export default Portfolio; // --- Member 6 END ---

//Member 7 – App Routing (Main App.jsx) // --- Member 7 START --- // File: App.jsx import { BrowserRouter as Router, Routes, Route } from "react-router-dom"; import Signup from "./Signup"; import OTPVerification from "./OTPVerification"; import DashboardHeader from "./DashboardHeader"; import MarketIndices from "./MarketIndices"; import StockSearch from "./StockSearch"; import StockDetail from "./StockDetail"; import Portfolio from "./Portfolio";

function App() { return ( <Route path="/signup" element={} /> <Route path="/verify-otp" element={} /> <Route path="/dashboard" element={

} /> <Route path="/stock/:symbol" element={} /> <Route path="/portfolio" element={} /> <Route path="*" element={} /> ); }
export default App;
