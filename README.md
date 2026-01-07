import React, { useState, useEffect, useRef } from 'react';
import { Lock, Music, Volume2, VolumeX, ArrowRight, Sparkles } from 'lucide-react'; // ←未使用を削除

export default function App() {
  const [step, setStep] = useState('intro');
  const [password, setPassword] = useState('');
  const [error, setError] = useState(false);
  const [currentIndex, setCurrentIndex] = useState(0);
  const [isPlaying, setIsPlaying] = useState(false);
  const [currentSong, setCurrentSong] = useState(0);

  const audioRef = useRef(null);
  const touchStart = useRef(0);
  const touchEnd = useRef(0);

  useEffect(() => {
    const href =
      "https://fonts.googleapis.com/css2?family=Shippori+Mincho:wght@400;700&family=Cormorant+Garamond:ital,wght@0,300;0,600;1,400&display=swap";

    // 既にあれば追加しない（StrictMode対策）
    const existing = document.querySelector(`link[href="${href}"]`);
    if (existing) return;

    const link = document.createElement('link');
    link.href = href;
    link.rel = "stylesheet";
    document.head.appendChild(link);
  }, []);

  // ★曲が切り替わったら、再生状態なら自動で再生する
  useEffect(() => {
    const a = audioRef.current;
    if (!a) return;
    if (!isPlaying) return;

    a.play().catch(() => {
      // iOS等でユーザー操作が必要な場合はここに落ちる（想定内）
    });
  }, [currentSong, isPlaying]);

  const handleLogin = () => {
    if (password === CONFIG.password) {
      setStep('gallery');
      setTimeout(() => {
        const a = audioRef.current;
        if (!a) return;
        a.play()
          .then(() => setIsPlaying(true))
          .catch(() => console.log("User interaction required"));
      }, 200);
    } else {
      setError(true);
      setPassword('');
      setTimeout(() => setError(false), 2000);
    }
  };

  const toggleMusic = () => {
    const a = audioRef.current;
    if (!a) return;

    if (a.paused) {
      a.play().then(() => setIsPlaying(true)).catch(() => {});
    } else {
      a.pause();
      setIsPlaying(false);
    }
  };

  // ...（中略：同じ）

  // ギャラリーのスライダー部分で easing を style に移す
  // className から cubic-bezier(...) を消して ↓を追加
  // style={{ transitionTimingFunction: 'cubic-bezier(0.16, 1, 0.3, 1)', transform: ... }}

  // 例：
  // <div
  //   className="flex transition-transform duration-1000 w-full h-full items-center will-change-transform"
  //   style={{
  //     transitionTimingFunction: 'cubic-bezier(0.16, 1, 0.3, 1)',
  //     transform: `translateX(calc(10% - ${currentIndex * 80}%))`
  //   }}
  // >

  // img onErrorの安全版（JSでもOK、TSでもOK）
  // onError={(e) => { e.currentTarget.src = "https://via.placeholder.com/600x600?text=Memory"; }}

  // パスワード入力（数字キーボードに寄せる）
  // <input
  //   type="password"
  //   inputMode="numeric"
  //   pattern="\d*"
  //   ...
  // />
}
