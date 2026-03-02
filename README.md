import React, { useState, useEffect, useRef } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { motion } from "framer-motion";
import { Play, Pause, RotateCcw } from "lucide-react";

export default function TimerApp() {
  const [totalSeconds, setTotalSeconds] = useState(0);
  const [hours, setHours] = useState(0);
  const [minutes, setMinutes] = useState(25);
  const [secs, setSecs] = useState(0);
  const [isRunning, setIsRunning] = useState(false);
  const intervalRef = useRef(null);
  const audioRef = useRef(null);

  useEffect(() => {
    audioRef.current = new Audio(
      "https://actions.google.com/sounds/v1/alarms/alarm_clock.ogg"
    );
  }, []);

  useEffect(() => {
    if (isRunning) {
      intervalRef.current = setInterval(() => {
        setTotalSeconds((prev) => {
          if (prev <= 1) {
            clearInterval(intervalRef.current);
            setIsRunning(false);
            audioRef.current?.play();
            return 0;
          }
          return prev - 1;
        });
      }, 1000);
    }
    return () => clearInterval(intervalRef.current);
  }, [isRunning]);

  const formatTime = (seconds) => {
    const h = Math.floor(seconds / 3600);
    const m = Math.floor((seconds % 3600) / 60);
    const s = seconds % 60;
    return `${String(h).padStart(2, "0")}:${String(m).padStart(2, "0")}:${String(s).padStart(2, "0")}`;
  };

  const handleStartPause = () => {
    if (totalSeconds > 0) setIsRunning(!isRunning);
  };

  const handleReset = () => {
    clearInterval(intervalRef.current);
    setIsRunning(false);
    const newTotal = hours * 3600 + minutes * 60 + secs;
    setTotalSeconds(newTotal);
  };

  const handleSetTime = () => {
    const newTotal = hours * 3600 + minutes * 60 + secs;
    setTotalSeconds(newTotal);
    setIsRunning(false);
  };

  const initialTotal = hours * 3600 + minutes * 60 + secs || 1;
  const progress = (totalSeconds / initialTotal) * 100;

  return (
    <div className="min-h-screen flex items-center justify-center bg-gradient-to-br from-white/20 to-white/5 backdrop-blur-3xl p-6">
      <Card className="w-full max-w-md bg-white/10 backdrop-blur-2xl border border-white/30 shadow-2xl rounded-3xl">
        <CardContent className="p-10 flex flex-col items-center gap-8">
          <motion.div
            initial={{ scale: 0.7, opacity: 0 }}
            animate={{ scale: 1, opacity: 1 }}
            transition={{ duration: 0.6 }}
            className="text-4xl font-extrabold tracking-widest text-black drop-shadow-lg"
          >
            {formatTime(totalSeconds)}
          </motion.div>

          <div className="w-full bg-white/20 rounded-full h-3 overflow-hidden backdrop-blur-xl">
            <div
              className="bg-white/70 h-full transition-all duration-500"
              style={{ width: `${progress}%` }}
            />
          </div>

          <div className="flex gap-4">
            <motion.div
              whileHover={{ scale: 1.2, rotate: 5 }}
              whileTap={{ scale: 0.8, rotate: -10 }}
              animate={{ rotate: isRunning ? [0, 3, -3, 0] : 0 }}
              transition={{ repeat: isRunning ? Infinity : 0, duration: 0.6 }}
            >
              <Button
                onClick={handleStartPause}
                className="rounded-2xl px-6 py-2 flex gap-2 bg-white/20 backdrop-blur-xl border border-white/40 text-black"
              >
                {isRunning ? <Pause size={18} /> : <Play size={18} />}
                {isRunning ? "Pause" : "Start"}
              </Button>
            </motion.div>

            <motion.div
              whileHover={{ scale: 1.15, rotate: -5 }}
              whileTap={{ scale: 0.75, rotate: 12 }}
            >
              <Button
                variant="outline"
                onClick={handleReset}
                className="rounded-2xl px-6 py-2 flex gap-2 bg-white/10 backdrop-blur-xl border border-white/30 text-black"
              >
                <RotateCcw size={18} />
                Reset
              </Button>
            </motion.div>
          </div>

          <div className="grid grid-cols-3 gap-3 w-full">
            <Input
              type="number"
              min="0"
              value={hours}
              onChange={(e) => setHours(Number(e.target.value))}
              placeholder="HH"
              className="rounded-xl bg-white/20 backdrop-blur-xl border-white/30 text-black"
            />
            <Input
              type="number"
              min="0"
              value={minutes}
              onChange={(e) => setMinutes(Number(e.target.value))}
              placeholder="MM"
              className="rounded-xl bg-white/20 backdrop-blur-xl border-white/30 text-black"
            />
            <Input
              type="number"
              min="0"
              value={secs}
              onChange={(e) => setSecs(Number(e.target.value))}
              placeholder="SS"
              className="rounded-xl bg-white/20 backdrop-blur-xl border-white/30 text-black"
            />
          </div>

          <Button
            onClick={handleSetTime}
            className="w-full rounded-2xl bg-white/30 backdrop-blur-xl border border-white/40 text-black"
          >
            Set Time
          </Button>
        </CardContent>
      </Card>
    </div>
  );
}
