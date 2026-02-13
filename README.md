# Lumino
An introvert friendly aesthetic app only made for people who are scared to show their creativity or style or aesthetics. make friends be with new ppl talk to them.
import { useState, useEffect } from "react";
import useUser from "@/utils/useUser";
import { Sparkles, Plus, User, Grid3x3, LogOut } from "lucide-react";

export default function HomePage() {
  const { data: user, loading: userLoading } = useUser();
  const [profile, setProfile] = useState(null);
  const [echoes, setEchoes] = useState([]);
  const [loading, setLoading] = useState(true);
  const [filter, setFilter] = useState("all");

  useEffect(() => {
    if (!userLoading && !user) {
      if (typeof window !== "undefined") {
        window.location.href = "/account/signin";
      }
    }
  }, [user, userLoading]);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const [profileRes, echoesRes] = await Promise.all([
          fetch("/api/profile"),
          fetch(`/api/echoes?type=${filter}`),
        ]);

        if (profileRes.ok) {
          const profileData = await profileRes.json();
          if (!profileData.profile) {
            if (typeof window !== "undefined") {
              window.location.href = "/onboarding";
            }
            return;
          }
          setProfile(profileData.profile);
        }

        if (echoesRes.ok) {
          const echoesData = await echoesRes.json();
          setEchoes(echoesData.echoes || []);
        }
      } catch (err) {
        console.error(err);
      } finally {
        setLoading(false);
      }
    };

    if (user) {
      fetchData();
    }
  }, [user, filter]);

  if (userLoading || loading) {
    return (
      <div className="min-h-screen flex items-center justify-center bg-gray-50 dark:bg-[#0A0A0A]">
        <p className="text-gray-600 dark:text-gray-400 font-jetbrains-mono">
          Loading...
        </p>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gray-50 dark:bg-[#0A0A0A]">
      {/* Header */}
      <header className="sticky top-0 z-50 bg-white dark:bg-[#1E1E1E] border-b border-gray-200 dark:border-gray-800">
        <div className="max-w-4xl mx-auto px-4 py-4 flex items-center justify-between">
          <h1 className="text-2xl font-bold text-gray-900 dark:text-gray-100 font-jetbrains-mono">
            Lumino
          </h1>

          <div className="flex items-center gap-3">
            <a
              href="/echo/create"
              className="p-2 hover:bg-gray-100 dark:hover:bg-gray-800 rounded-lg transition-colors"
            >
              <Plus className="text-gray-900 dark:text-gray-100" size={20} />
            </a>
            <a
              href="/pods"
              className="p-2 hover:bg-gray-100 dark:hover:bg-gray-800 rounded-lg transition-colors"
            >
              <Grid3x3 className="text-gray-900 dark:text-gray-100" size={20} />
            </a>
            <a
              href="/profile"
              className="p-2 hover:bg-gray-100 dark:hover:bg-gray-800 rounded-lg transition-colors"
            >
              <User className="text-gray-900 dark:text-gray-100" size={20} />
            </a>
            <a
              href="/account/logout"
              className="p-2 hover:bg-gray-100 dark:hover:bg-gray-800 rounded-lg transition-colors"
            >
              <LogOut className="text-gray-900 dark:text-gray-100" size={20} />
            </a>
          </div>
        </div>
      </header>

      {/* Main Content */}
      <main className="max-w-4xl mx-auto px-4 py-8">
        {/* AI Companion Badge */}
        {profile && (
          <div className="mb-6 flex items-center gap-2 text-sm text-gray-600 dark:text-gray-400 font-jetbrains-mono">
            <Sparkles size={16} className="text-gray-500 dark:text-gray-500" />
            <span>
              Your AI companion:{" "}
              <strong className="text-gray-900 dark:text-gray-100">
                {profile.ai_mode === "axon" ? "Axon" : "Chroma"}
              </strong>
            </span>
          </div>
        )}

        {/* Filter Tabs */}
        <div className="mb-8 flex gap-2 bg-white dark:bg-[#1E1E1E] rounded-xl p-1 border border-gray-200 dark:border-gray-800">
          <button
            onClick={() => setFilter("all")}
            className={`flex-1 px-4 py-2 rounded-lg font-jetbrains-mono text-sm transition-colors ${
              filter === "all"
                ? "bg-gray-900 dark:bg-gray-700 text-white"
                : "text-gray-600 dark:text-gray-400 hover:bg-gray-100 dark:hover:bg-gray-800"
            }`}
          >
            All Echoes
          </button>
          <button
            onClick={() => setFilter("friends")}
            className={`flex-1 px-4 py-2 rounded-lg font-jetbrains-mono text-sm transition-colors ${
              filter === "friends"
                ? "bg-gray-900 dark:bg-gray-700 text-white"
                : "text-gray-600 dark:text-gray-400 hover:bg-gray-100 dark:hover:bg-gray-800"
            }`}
          >
            Friends
          </button>
        </div>

        {/* Echoes Feed */}
        {echoes.length === 0 ? (
          <div className="text-center py-16">
            <p className="text-gray-600 dark:text-gray-400 font-jetbrains-mono mb-4">
              No echoes yet. Start by creating your first one.
            </p>
            <a
              href="/echo/create"
              className="inline-flex items-center gap-2 px-6 py-3 bg-gray-900 dark:bg-gray-700 hover:bg-gray-800 dark:hover:bg-gray-600 text-white rounded-xl font-jetbrains-mono transition-colors"
            >
              <Plus size={20} />
              Create Echo
            </a>
          </div>
        ) : (
          <div className="space-y-6">
            {echoes.map((echo) => (
              <div
                key={echo.id}
                className="bg-gray-100 dark:bg-[#111111] rounded-2xl overflow-hidden border border-gray-200 dark:border-gray-900"
              >
                {/* Echo Header */}
                <div className="p-4 flex items-center gap-3 bg-white dark:bg-[#1A1A1A]">
                  <div className="w-10 h-10 bg-gradient-to-br from-gray-700 to-gray-900 rounded-full flex items-center justify-center">
                    <span className="text-white font-semibold text-sm">
                      {echo.username?.[0]?.toUpperCase()}
                    </span>
                  </div>
                  <div>
                    <p className="font-semibold text-gray-900 dark:text-gray-100 font-jetbrains-mono">
                      {echo.username}
                    </p>
                    <p className="text-xs text-gray-500 dark:text-gray-500 font-jetbrains-mono">
                      {new Date(echo.created_at).toLocaleDateString()}
                    </p>
                  </div>
                </div>

                {/* Echo Image */}
                <img
                  src={echo.image_url}
                  alt={echo.caption || "Echo"}
                  className="w-full h-auto max-h-[600px] object-cover"
                />

                {/* Echo Content */}
                <div className="p-4 space-y-3 bg-white dark:bg-[#1A1A1A]">
                  {echo.caption && (
                    <p className="text-gray-900 dark:text-gray-100 font-jetbrains-mono">
                      {echo.caption}
                    </p>
                  )}
                  {echo.tags && echo.tags.length > 0 && (
                    <div className="flex flex-wrap gap-2">
                      {echo.tags.map((tag, idx) => (
                        <span
                          key={idx}
                          className="px-3 py-1 bg-gray-200 dark:bg-gray-800 text-gray-700 dark:text-gray-300 rounded-full text-sm font-jetbrains-mono"
                        >
                          #{tag}
                        </span>
                      ))}
                    </div>
                  )}
                </div>
              </div>
            ))}
          </div>
        )}
      </main>
    </div>
  );
}
