# MAYA
マヤ暦KINナンバーと周期
import React, { useMemo, useState } from "react";
import { Card, CardContent, CardHeader, CardTitle } from "@/components/ui/card";
import { Input } from "@/components/ui/input";
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from "@/components/ui/table";
import { Badge } from "@/components/ui/badge";
import { CalendarDays, Sparkles } from "lucide-react";

type YMD = { year: number; month: number; day: number };
type CycleColor = "赤" | "白" | "青" | "黄";
type KinYearRow = {
  years: [number, number, number];
  months: [
    number,
    number,
    number,
    number,
    number,
    number,
    number,
    number,
    number,
    number,
    number,
    number,
  ];
};

type RowItem = {
  age: number;
  start: string;
  end: string;
  yearKin: number;
  cycleYear: number;
  yearColor: CycleColor;
  seal: string;
  wavespell: string;
};

const seals = [
  "赤い龍",
  "白い風",
  "青い夜",
  "黄色い種",
  "赤い蛇",
  "白い世界の橋渡し",
  "青い手",
  "黄色い星",
  "赤い月",
  "白い犬",
  "青い猿",
  "黄色い人",
  "赤い空歩く人",
  "白い魔法使い",
  "青い鷲",
  "黄色い戦士",
  "赤い地球",
  "白い鏡",
  "青い嵐",
  "黄色い太陽",
] as const;

const wavespells = [
  "赤い龍",
  "白い魔法使い",
  "青い手",
  "黄色い太陽",
  "赤い空歩く人",
  "白い世界の橋渡し",
  "青い嵐",
  "黄色い人",
  "赤い蛇",
  "白い鏡",
  "青い猿",
  "黄色い種",
  "赤い地球",
  "白い犬",
  "青い夜",
  "黄色い戦士",
  "赤い月",
  "白い風",
  "青い鷲",
  "黄色い星",
] as const;

const kinYearRows: KinYearRow[] = [
  { years: [1910, 1962, 2014], months: [62, 93, 121, 152, 182, 213, 243, 14, 45, 75, 106, 136] },
  { years: [1911, 1963, 2015], months: [167, 198, 226, 257, 27, 58, 88, 119, 150, 180, 211, 241] },
  { years: [1912, 1964, 2016], months: [12, 43, 71, 102, 132, 163, 193, 224, 255, 25, 56, 86] },
  { years: [1913, 1965, 2017], months: [117, 148, 176, 207, 237, 8, 38, 69, 100, 130, 161, 191] },
  { years: [1914, 1966, 2018], months: [222, 253, 21, 52, 82, 113, 143, 174, 205, 235, 6, 36] },
  { years: [1915, 1967, 2019], months: [67, 98, 126, 157, 187, 218, 248, 19, 50, 80, 111, 141] },
  { years: [1916, 1968, 2020], months: [172, 203, 231, 2, 32, 63, 93, 124, 155, 185, 216, 246] },
  { years: [1917, 1969, 2021], months: [17, 48, 76, 107, 137, 168, 198, 229, 0, 30, 61, 91] },
  { years: [1918, 1970, 2022], months: [122, 153, 181, 212, 242, 13, 43, 74, 105, 135, 166, 196] },
  { years: [1919, 1971, 2023], months: [227, 258, 26, 57, 87, 118, 148, 179, 210, 240, 11, 41] },
  { years: [1920, 1972, 2024], months: [72, 103, 131, 162, 192, 223, 253, 24, 55, 85, 116, 146] },
  { years: [1921, 1973, 2025], months: [177, 208, 236, 7, 37, 68, 98, 129, 160, 190, 221, 251] },
  { years: [1922, 1974, 2026], months: [22, 53, 81, 112, 142, 173, 203, 234, 5, 35, 66, 96] },
  { years: [1923, 1975, 2027], months: [127, 158, 186, 217, 247, 18, 48, 79, 110, 140, 171, 201] },
  { years: [1924, 1976, 2028], months: [232, 3, 31, 62, 92, 123, 153, 184, 215, 245, 16, 46] },
  { years: [1925, 1977, 2029], months: [77, 108, 136, 167, 197, 228, 258, 29, 60, 90, 121, 151] },
  { years: [1926, 1978, 2030], months: [182, 213, 241, 12, 42, 73, 103, 134, 165, 195, 226, 256] },
  { years: [1927, 1979, 2031], months: [27, 58, 86, 117, 147, 178, 208, 239, 10, 40, 71, 101] },
  { years: [1928, 1980, 2032], months: [132, 163, 191, 222, 252, 23, 53, 84, 115, 145, 176, 206] },
  { years: [1929, 1981, 2033], months: [237, 8, 36, 67, 97, 128, 158, 189, 220, 250, 21, 51] },
  { years: [1930, 1982, 2034], months: [82, 113, 141, 172, 202, 233, 3, 34, 65, 95, 126, 156] },
  { years: [1931, 1983, 2035], months: [187, 218, 246, 17, 47, 78, 108, 139, 170, 200, 231, 1] },
  { years: [1932, 1984, 2036], months: [32, 63, 91, 122, 152, 183, 213, 244, 15, 45, 76, 106] },
  { years: [1933, 1985, 2037], months: [137, 168, 196, 227, 257, 28, 58, 89, 120, 150, 181, 211] },
  { years: [1934, 1986, 2038], months: [242, 13, 41, 72, 102, 133, 163, 194, 225, 255, 26, 56] },
  { years: [1935, 1987, 2039], months: [87, 118, 146, 177, 207, 238, 8, 39, 70, 100, 131, 161] },
  { years: [1936, 1988, 2040], months: [192, 223, 251, 22, 52, 83, 113, 144, 175, 205, 236, 6] },
  { years: [1937, 1989, 2041], months: [37, 68, 96, 127, 157, 188, 218, 249, 20, 50, 81, 111] },
  { years: [1938, 1990, 2042], months: [142, 173, 201, 232, 2, 33, 63, 94, 125, 155, 186, 216] },
  { years: [1939, 1991, 2043], months: [247, 18, 46, 77, 107, 138, 168, 199, 230, 0, 31, 61] },
  { years: [1940, 1992, 2044], months: [92, 123, 151, 182, 212, 243, 13, 44, 75, 105, 136, 166] },
  { years: [1941, 1993, 2045], months: [197, 228, 256, 27, 57, 88, 118, 149, 180, 210, 241, 11] },
  { years: [1942, 1994, 2046], months: [42, 73, 101, 132, 162, 193, 223, 254, 25, 55, 86, 116] },
  { years: [1943, 1995, 2047], months: [147, 178, 206, 237, 7, 38, 68, 99, 130, 160, 191, 221] },
  { years: [1944, 1996, 2048], months: [252, 23, 51, 82, 112, 143, 173, 204, 235, 5, 36, 66] },
  { years: [1945, 1997, 2049], months: [97, 128, 156, 187, 217, 248, 18, 49, 80, 110, 141, 171] },
  { years: [1946, 1998, 2050], months: [202, 233, 1, 32, 62, 93, 123, 154, 185, 215, 246, 16] },
  { years: [1947, 1999, 2051], months: [47, 78, 106, 137, 167, 198, 228, 259, 30, 60, 91, 121] },
  { years: [1948, 2000, 2052], months: [152, 183, 211, 242, 12, 43, 73, 104, 135, 165, 196, 226] },
  { years: [1949, 2001, 2053], months: [257, 28, 56, 87, 117, 148, 178, 209, 240, 10, 41, 71] },
  { years: [1950, 2002, 2054], months: [102, 133, 161, 192, 222, 253, 23, 54, 85, 115, 146, 176] },
  { years: [1951, 2003, 2055], months: [207, 238, 6, 37, 67, 98, 128, 159, 190, 220, 251, 21] },
  { years: [1952, 2004, 2056], months: [52, 83, 111, 142, 172, 203, 233, 4, 35, 65, 96, 126] },
  { years: [1953, 2005, 2057], months: [157, 188, 216, 247, 17, 48, 78, 109, 140, 170, 201, 231] },
  { years: [1954, 2006, 2058], months: [2, 33, 61, 92, 122, 153, 183, 214, 245, 15, 46, 76] },
  { years: [1955, 2007, 2059], months: [107, 138, 166, 197, 227, 258, 28, 59, 90, 120, 151, 181] },
  { years: [1956, 2008, 2060], months: [212, 243, 11, 42, 72, 103, 133, 164, 195, 225, 256, 26] },
  { years: [1957, 2009, 2061], months: [57, 88, 116, 147, 177, 208, 238, 9, 40, 70, 101, 131] },
  { years: [1958, 2010, 2062], months: [162, 193, 221, 252, 22, 53, 83, 114, 145, 175, 206, 236] },
  { years: [1959, 2011, 2063], months: [7, 38, 66, 97, 127, 158, 188, 219, 250, 20, 51, 81] },
  { years: [1960, 2012, 2064], months: [112, 143, 171, 202, 232, 3, 33, 64, 95, 125, 156, 186] },
  { years: [1961, 2013, 2065], months: [217, 248, 16, 47, 77, 108, 138, 169, 200, 230, 1, 31] },
];

const fillClassByColor: Record<CycleColor, string> = {
  赤: "bg-red-100 border-red-200",
  白: "bg-white border-slate-200",
  青: "bg-blue-100 border-blue-200",
  黄: "bg-yellow-100 border-yellow-200",
};

const rowClassByColor: Record<CycleColor, string> = {
  赤: "bg-red-50",
  白: "bg-white",
  青: "bg-blue-50",
  黄: "bg-yellow-50",
};

function badgeClassByName(name: string) {
  if (!name) return "bg-white text-black";
  if (name.includes("赤")) return "bg-red-100 text-red-700 border-red-200";
  if (name.includes("青")) return "bg-blue-100 text-blue-700 border-blue-200";
  if (name.includes("黄")) return "bg-yellow-100 text-yellow-800 border-yellow-200";
  return "bg-white text-black border-slate-200";
}

function parseFlexibleDate(value: string): YMD | null {
  const normalized = value.trim().replace(/\./g, "/").replace(/-/g, "/");
  const match = normalized.match(/^(\d{4})\/(\d{1,2})\/(\d{1,2})$/);
  if (!match) return null;

  const [, y, m, d] = match;
  const year = Number(y);
  const month = Number(m);
  const day = Number(d);

  const test = new Date(Date.UTC(year, month - 1, day));
  if (
    test.getUTCFullYear() !== year ||
    test.getUTCMonth() + 1 !== month ||
    test.getUTCDate() !== day
  ) {
    return null;
  }

  return { year, month, day };
}

function formatDate(ymd: YMD) {
  return `${ymd.year}/${String(ymd.month).padStart(2, "0")}/${String(ymd.day).padStart(2, "0")}`;
}

function isLeapYear(year: number) {
  return year % 400 === 0 || (year % 4 === 0 && year % 100 !== 0);
}

function kinWrap(n: number) {
  return ((n - 1) % 260 + 260) % 260 + 1;
}

function getKinYearRow(year: number) {
  return kinYearRows.find((row) => row.years.includes(year));
}

function getBirthKin(ymd: YMD) {
  const row = getKinYearRow(ymd.year);
  if (!row) return 0;

  const base = row.months[ymd.month - 1];
  let kin = base + ymd.day;

  if (isLeapYear(ymd.year) && ymd.month === 3) kin += 1;
  if (kin > 260) kin -= 260;

  return kin;
}

function getColorFromWavespell(wavespell: string): CycleColor {
  if (wavespell.includes("赤")) return "赤";
  if (wavespell.includes("青")) return "青";
  if (wavespell.includes("黄")) return "黄";
  return "白";
}

function getSeal(kin: number) {
  return seals[(kin - 1) % 20];
}

function getWavespell(kin: number) {
  return wavespells[Math.floor((kin - 1) / 13)];
}

function getTone(kin: number) {
  return ((kin - 1) % 13) + 1;
}

function getToneFromBirthDate(ymd: YMD) {
  return getTone(getBirthKin(ymd));
}

function lastDayOfMonth(year: number, month: number) {
  return new Date(Date.UTC(year, month, 0)).getUTCDate();
}

function addYearsSafe(base: YMD, years: number): YMD {
  const targetYear = base.year + years;
  let targetDay = base.day;

  if (base.month === 2 && base.day === 29 && !isLeapYear(targetYear)) {
    targetDay = 28;
  } else {
    targetDay = Math.min(base.day, lastDayOfMonth(targetYear, base.month));
  }

  return {
    year: targetYear,
    month: base.month,
    day: targetDay,
  };
}

function shouldAddLeapAdjustmentForYear(base: YMD, targetYear: number) {
  return isLeapYear(base.year) && base.month >= 3 && !isLeapYear(targetYear);
}

function getYearKin(base: YMD, birthKin: number, age: number) {
  const targetYear = base.year + age;
  const leapAdjustment = shouldAddLeapAdjustmentForYear(base, targetYear) ? 1 : 0;
  return kinWrap(birthKin + age * 105 + leapAdjustment);
}

function subtractOneDay(ymd: YMD): YMD {
  const d = new Date(Date.UTC(ymd.year, ymd.month - 1, ymd.day));
  d.setUTCDate(d.getUTCDate() - 1);
  return {
    year: d.getUTCFullYear(),
    month: d.getUTCMonth() + 1,
    day: d.getUTCDate(),
  };
}

function runSelfChecks() {
  const checks = [
    getBirthKin({ year: 1969, month: 6, day: 16 }) === 184,
    getBirthKin({ year: 2007, month: 3, day: 10 }) === 176,
    getBirthKin({ year: 2012, month: 3, day: 10 }) === 182,
    getBirthKin({ year: 2026, month: 3, day: 10 }) === 91,
    kinWrap(getBirthKin({ year: 1969, month: 6, day: 16 }) + 105) === 29,
    shouldAddLeapAdjustmentForYear({ year: 2000, month: 3, day: 1 }, 2001) === true,
    shouldAddLeapAdjustmentForYear({ year: 2000, month: 3, day: 1 }, 2004) === false,
    getYearKin({ year: 2000, month: 3, day: 1 }, 100, 1) === kinWrap(100 + 105 + 1),
    parseFlexibleDate("1995/03/12")?.year === 1995,
    parseFlexibleDate("1995-03-12")?.month === 3,
    parseFlexibleDate("1995/02/30") === null,
    isLeapYear(2000) === true,
    isLeapYear(1900) === false,
    kinWrap(261) === 1,
    kinWrap(0) === 260,
    getSeal(1) === "赤い龍",
    getWavespell(1) === "赤い龍",
    getColorFromWavespell("赤い龍") === "赤",
    getColorFromWavespell("白い魔法使い") === "白",
    getColorFromWavespell("青い手") === "青",
    getColorFromWavespell("黄色い太陽") === "黄",
    getTone(13) === 13,
    getTone(14) === 1,
    getTone(184) === 2,
    getTone(29) === 3,
    formatDate(addYearsSafe({ year: 2000, month: 2, day: 29 }, 1)) === "2001/02/28",
    formatDate(subtractOneDay({ year: 2001, month: 3, day: 1 })) === "2001/02/28",
    getKinYearRow(2007)?.months[2] === 166,
    getKinYearRow(2012)?.months[2] === 171,
    getKinYearRow(2026)?.months[2] === 81,
  ];

  return checks.every(Boolean);
}

void runSelfChecks();

function MobileYearCard({ row }: { row: RowItem }) {
  return (
    <Card className={`rounded-2xl border shadow-sm ${rowClassByColor[row.yearColor]}`}>
      <CardContent className="p-4 space-y-3">
        <div className="flex items-center justify-between gap-3">
          <div>
            <div className="text-xs text-muted-foreground">年齢</div>
            <div className="text-xl font-bold">{row.age}歳</div>
          </div>
          <div className="flex gap-2">
            <Badge variant="secondary" className={fillClassByColor[row.yearColor]}>
              {row.yearColor}
            </Badge>
            <Badge variant="outline">音 {row.cycleYear}</Badge>
          </div>
        </div>

        <div className="grid grid-cols-2 gap-3 text-sm">
          <div className="rounded-xl border bg-white/70 p-3">
            <div className="text-xs text-muted-foreground">期間開始</div>
            <div className="font-medium">{row.start}</div>
          </div>
          <div className="rounded-xl border bg-white/70 p-3">
            <div className="text-xs text-muted-foreground">期間終了</div>
            <div className="font-medium">{row.end}</div>
          </div>
          <div className="rounded-xl border bg-white/70 p-3 col-span-2">
            <div className="text-xs text-muted-foreground">年のKINナンバー</div>
            <div className="text-lg font-semibold">{row.yearKin}</div>
          </div>
        </div>

        <div className="space-y-2">
          <div>
            <div className="mb-1 text-xs text-muted-foreground">太陽の紋章</div>
            <div className={`rounded-xl border px-3 py-2 font-medium ${badgeClassByName(row.seal)}`}>
              {row.seal}
            </div>
          </div>
          <div>
            <div className="mb-1 text-xs text-muted-foreground">ウェイブスペル</div>
            <div className={`rounded-xl border px-3 py-2 font-medium ${badgeClassByName(row.wavespell)}`}>
              {row.wavespell}
            </div>
          </div>
        </div>
      </CardContent>
    </Card>
  );
}

export default function MayaCalendarApp() {
  const [birthDateText, setBirthDateText] = useState("1995/03/12");
  const parsedBirthDate = parseFlexibleDate(birthDateText);

  const summary = useMemo(() => {
    if (!parsedBirthDate) return null;
    const birthKin = getBirthKin(parsedBirthDate);
    const tone = getToneFromBirthDate(parsedBirthDate);
    const startColor = getColorFromWavespell(getWavespell(birthKin));
    return {
      birthKin,
      tone,
      startColor,
      seal: getSeal(birthKin),
      wavespell: getWavespell(birthKin),
    };
  }, [parsedBirthDate]);

  const rows = useMemo<RowItem[]>(() => {
    if (!parsedBirthDate || !summary) return [];

    return Array.from({ length: 101 }, (_, age) => {
      const yearKin = getYearKin(parsedBirthDate, summary.birthKin, age);
      const start = addYearsSafe(parsedBirthDate, age);
      const next = addYearsSafe(parsedBirthDate, age + 1);
      const end = subtractOneDay(next);

      return {
        age,
        start: formatDate(start),
        end: formatDate(end),
        yearKin,
        cycleYear: getTone(yearKin),
        yearColor: getColorFromWavespell(getWavespell(yearKin)),
        seal: getSeal(yearKin),
        wavespell: getWavespell(yearKin),
      };
    });
  }, [parsedBirthDate, summary]);

  return (
    <div className="min-h-screen bg-gradient-to-b from-white to-slate-50 p-3 md:p-8">
      <div className="mx-auto max-w-7xl space-y-5 md:space-y-6">
        <div className="space-y-2">
          <div className="inline-flex items-center gap-2 rounded-full border bg-white px-3 py-1 text-sm shadow-sm">
            <Sparkles className="h-4 w-4" />
            マヤ暦 13年周期アプリ
          </div>
          <h1 className="text-2xl md:text-3xl font-bold tracking-tight">生年月日だけで自動表示</h1>
          <p className="text-sm text-muted-foreground">
            Excel版のロジックに合わせて、KINナンバー・色・音・太陽の紋章・ウェイブスペル・0歳〜100歳までの年表を表示します。
          </p>
        </div>

        <div className="grid gap-5 lg:grid-cols-[360px_1fr] xl:grid-cols-[380px_1fr]">
          <Card className="rounded-3xl shadow-sm order-1">
            <CardHeader className="pb-3">
              <CardTitle className="flex items-center gap-2 text-lg">
                <CalendarDays className="h-5 w-5" />
                入力
              </CardTitle>
            </CardHeader>
            <CardContent className="space-y-4">
              <div className="space-y-2">
                <label className="text-sm font-medium">生年月日</label>
                <Input
                  type="text"
                  placeholder="例: 1995/03/12"
                  value={birthDateText}
                  onChange={(e) => setBirthDateText(e.target.value)}
                  className="h-11 text-base"
                />
                <p className="text-xs text-muted-foreground">
                  1995/03/12 のように入力してください。例: 1969/06/16 → KIN 184
                </p>
              </div>

              {!parsedBirthDate && (
                <div className="rounded-xl border border-red-200 bg-red-50 px-3 py-2 text-sm text-red-700">
                  日付の形式が正しくありません。例: 1995/03/12
                </div>
              )}

              {summary && (
                <div className="rounded-2xl border bg-slate-50 p-4 space-y-3">
                  <div className="grid grid-cols-2 gap-3 text-sm">
                    <div className="rounded-xl border bg-white p-3">
                      <div className="text-muted-foreground">KINナンバー</div>
                      <div className="text-xl font-semibold">{summary.birthKin}</div>
                    </div>
                    <div className="rounded-xl border bg-white p-3">
                      <div className="text-muted-foreground">音</div>
                      <div className="text-xl font-semibold">{summary.tone}</div>
                    </div>
                  </div>

                  <div>
                    <div className="mb-1 text-xs text-muted-foreground">出生ウェイブスペルの色</div>
                    <Badge variant="secondary" className={fillClassByColor[summary.startColor]}>
                      {summary.startColor}
                    </Badge>
                  </div>

                  <div>
                    <div className="mb-1 text-xs text-muted-foreground">太陽の紋章</div>
                    <div className={`rounded-xl border px-3 py-2 font-medium ${badgeClassByName(summary.seal)}`}>
                      {summary.seal}
                    </div>
                  </div>

                  <div>
                    <div className="mb-1 text-xs text-muted-foreground">ウェイブスペル</div>
                    <div className={`rounded-xl border px-3 py-2 font-medium ${badgeClassByName(summary.wavespell)}`}>
                      {summary.wavespell}
                    </div>
                  </div>
                </div>
              )}
            </CardContent>
          </Card>

          <Card className="rounded-3xl shadow-sm order-2">
            <CardHeader className="pb-3">
              <CardTitle className="text-lg">0歳〜100歳 年表</CardTitle>
            </CardHeader>
            <CardContent>
              <div className="hidden md:block max-h-[70vh] overflow-auto rounded-2xl border">
                <Table>
                  <TableHeader className="sticky top-0 z-10 bg-white">
                    <TableRow>
                      <TableHead>年齢</TableHead>
                      <TableHead>期間開始</TableHead>
                      <TableHead>期間終了</TableHead>
                      <TableHead>色</TableHead>
                      <TableHead>音（何年目）</TableHead>
                      <TableHead>年のKINナンバー</TableHead>
                      <TableHead>太陽の紋章</TableHead>
                      <TableHead>ウェイブスペル</TableHead>
                    </TableRow>
                  </TableHeader>
                  <TableBody>
                    {rows.map((row) => (
                      <TableRow key={row.age} className={rowClassByColor[row.yearColor]}>
                        <TableCell className="font-medium">{row.age}</TableCell>
                        <TableCell>{row.start}</TableCell>
                        <TableCell>{row.end}</TableCell>
                        <TableCell>
                          <Badge variant="secondary" className={fillClassByColor[row.yearColor]}>
                            {row.yearColor}
                          </Badge>
                        </TableCell>
                        <TableCell>{row.cycleYear}</TableCell>
                        <TableCell>{row.yearKin}</TableCell>
                        <TableCell>
                          <span className={`inline-block rounded-lg border px-2 py-1 font-medium ${badgeClassByName(row.seal)}`}>
                            {row.seal}
                          </span>
                        </TableCell>
                        <TableCell>
                          <span className={`inline-block rounded-lg border px-2 py-1 font-medium ${badgeClassByName(row.wavespell)}`}>
                            {row.wavespell}
                          </span>
                        </TableCell>
                      </TableRow>
                    ))}
                  </TableBody>
                </Table>
              </div>

              <div className="md:hidden space-y-3 max-h-[70vh] overflow-auto pr-1">
                {rows.map((row) => (
                  <MobileYearCard key={row.age} row={row} />
                ))}
              </div>
            </CardContent>
          </Card>
        </div>
      </div>
    </div>
  );
}
