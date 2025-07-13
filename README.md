import { useState } from "react"; import { Card, CardContent } from "@/components/ui/card"; import { Input } from "@/components/ui/input"; import { Button } from "@/components/ui/button"; import { Sparkles } from "lucide-react"; import { motion } from "framer-motion";

export default function ChatApp() { const [messages, setMessages] = useState([ { sender: "ai", text: "Hi! I am LOCHI AI. How can I help you today?" } ]); const [input, setInput] = useState(""); const [loading, setLoading] = useState(false);

async function sendMessage() { if (!input.trim()) return; const newMessages = [...messages, { sender: "user", text: input }]; setMessages(newMessages); setInput(""); setLoading(true);

try {
  const res = await fetch("https://api.openai.com/v1/chat/completions", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      "Authorization": `Bearer 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`
    },
    body: JSON.stringify({
      model: "gpt-4o-mini",
      messages: newMessages.map(m => ({ role: m.sender === "user" ? "user" : "assistant", content: m.text }))
    })
  });
  const data = await res.json();
  const aiReply = data.choices[0].message.content;
  setMessages([...newMessages, { sender: "ai", text: aiReply }]);
} catch (e) {
  console.error(e);
} finally {
  setLoading(false);
}

}

return ( <div className="min-h-screen bg-black flex flex-col items-center p-4"> <motion.h1 className="text-4xl font-bold text-pink-500 mb-4" initial={{ opacity: 0, y: -20 }} animate={{ opacity: 1, y: 0 }}> <Sparkles className="inline mr-2" /> LOCHI AI </motion.h1>

<div className="flex flex-col gap-2 w-full max-w-xl flex-1 overflow-auto mb-4">
    {messages.map((msg, i) => (
      <motion.div 
        key={i} 
        initial={{ opacity: 0, y: 10 }} 
        animate={{ opacity: 1, y: 0 }}>
        <Card className={`rounded-2xl shadow-md ${msg.sender === "ai" ? "border-pink-500" : "border-gray-700"} border bg-black text-white`}>
          <CardContent className="p-4">
            <p>{msg.text}</p>
          </CardContent>
        </Card>
      </motion.div>
    ))}
  </div>

  <div className="flex w-full max-w-xl items-center border border-pink-500 rounded-2xl overflow-hidden mb-2">
    <Input 
      className="bg-black text-white focus:ring-0 border-0" 
      placeholder="Type your message..." 
      value={input} 
      onChange={e => setInput(e.target.value)} 
      onKeyDown={e => e.key === 'Enter' && sendMessage()} 
    />
    <Button onClick={sendMessage} disabled={loading} className="bg-pink-500 hover:bg-pink-600 text-black rounded-none">Send</Button>
  </div>

  <p className="text-pink-500 text-xs">Developed By Rush Dewon</p>
</div>

); }

