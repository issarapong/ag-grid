# ห้องปฏิบัติการ AG Grid กับ Next.js

เอกสารฉบับนี้เป็นคู่มือภาษาไทยสำหรับฝึกสร้างแอปพลิเคชันเว็บด้วย [Next.js](https://nextjs.org/) และ [AG Grid](https://www.ag-grid.com/) ตั้งแต่พื้นฐานไปจนถึงหัวข้อระดับสูง โดยเนื้อหาถูกจัดเรียงเป็นลำดับห้องปฏิบัติการ (Lab) เพื่อให้สามารถลงมือทำตามได้จริงทีละขั้น

> **หมายเหตุ**: คู่มือนี้อ้างอิง Next.js 14 (App Router) และ AG Grid เวอร์ชันล่าสุดในช่วงต้นปี 2024 หากมีการเปลี่ยนแปลงเวอร์ชัน ให้ปรับคำสั่งติดตั้งและตัวเลือกต่าง ๆ ให้สอดคล้องกับเวอร์ชันที่ใช้งานจริง

## โครงสร้างภาพรวมของ Labs

| Lab | หัวข้อ | จุดประสงค์ |
| --- | --- | --- |
| 0 | เตรียมเครื่องมือ | ติดตั้ง Node.js, Git และเครื่องมือที่จำเป็น | 
| 1 | สร้างโปรเจ็กต์ Next.js | เรียนรู้โครงสร้างโปรเจ็กต์และการรัน dev server |
| 2 | ติดตั้งและแสดงผลตาราง AG Grid เบื้องต้น | ทำความรู้จักกับ GridOptions, คอลัมน์, และการกำหนดข้อมูล |
| 3 | ปรับแต่งคอลัมน์และสไตล์ | ใช้ cell renderer, value formatter, และ theme |
| 4 | ฟีเจอร์เชิงโต้ตอบ | เปิดใช้งาน sorting, filtering, pagination, selection |
| 5 | การทำงานฝั่งเซิร์ฟเวอร์ | ใช้ API Route ของ Next.js เพื่อดึงข้อมูลแบบ server-side | 
| 6 | ข้อมูลจำนวนมากและ Virtualization | ปรับแต่ง row model เพื่อรองรับข้อมูลจำนวนมาก |
| 7 | Integration ระดับสูง | ผสานกับ Zustand/Redux, Export, Charts, และ Accessibility |
| 8 | ทดสอบและ Deploy | เขียนเทสต์ E2E, ตรวจสอบ Lighthouse, และ Deploy ไปยัง Vercel |

แต่ละห้องปฏิบัติการจะประกอบด้วย

1. **เป้าหมาย** – สิ่งที่จะได้เรียนรู้
2. **การเตรียมก่อนเริ่ม** – ไฟล์หรือโค้ดตั้งต้นที่ต้องมี
3. **ขั้นตอนลงมือทำ** – คำสั่งและโค้ดทีละขั้น
4. **แบบฝึกหัดเพิ่มเติม** – การบ้านหรือแนวคิดต่อยอด

---

## Lab 0 – เตรียมเครื่องมือ

### เป้าหมาย
- ติดตั้ง Node.js 18 ขึ้นไป และตรวจสอบเครื่องมือพื้นฐาน
- ตั้งค่า VS Code (หรือ IDE ที่ชอบ) พร้อมส่วนเสริมสำหรับ TypeScript และ ESLint

### ขั้นตอน
1. ดาวน์โหลดและติดตั้ง [Node.js LTS](https://nodejs.org/en/) หรือใช้ [nvm](https://github.com/nvm-sh/nvm) เพื่อบริหารเวอร์ชัน
2. ตรวจสอบเวอร์ชันด้วย
   ```bash
   node -v
   npm -v
   ```
3. ติดตั้ง Git และตั้งค่า global config
   ```bash
   git config --global user.name "Your Name"
   git config --global user.email "you@example.com"
   ```
4. ติดตั้งส่วนเสริมใน VS Code ได้แก่ ESLint, Prettier, Tailwind CSS IntelliSense (ถ้าใช้ Tailwind)

### แบบฝึกหัดเพิ่มเติม
- ลองตั้งค่า `core.autocrlf` และ `core.editor` ใน Git ให้ตรงกับเครื่องของคุณ

---

## Lab 1 – สร้างโปรเจ็กต์ Next.js

### เป้าหมาย
- สร้างโปรเจ็กต์ Next.js รุ่น App Router พร้อม TypeScript
- เรียนรู้โครงสร้างโฟลเดอร์พื้นฐานของ `app/`

### ขั้นตอน
1. สร้างโปรเจ็กต์ใหม่
   ```bash
   npx create-next-app@latest ag-grid-lab --typescript --eslint --app --tailwind --src-dir
   ```
   ปรับตัวเลือกตามความต้องการ (หากไม่ใช้ Tailwind ให้ตัด `--tailwind` ออก)
2. เข้าไปในโฟลเดอร์โปรเจ็กต์และรันเซิร์ฟเวอร์พัฒนา
   ```bash
   cd ag-grid-lab
   npm run dev
   ```
3. เปิดเบราว์เซอร์ที่ `http://localhost:3000` ตรวจสอบว่าหน้า Welcome ของ Next.js แสดงผลถูกต้อง
4. สำรวจโครงสร้างโฟลเดอร์หลัก เช่น `app/layout.tsx`, `app/page.tsx`, `app/globals.css`

### แบบฝึกหัดเพิ่มเติม
- เปลี่ยนข้อความในหน้า `app/page.tsx` ให้เป็นภาษาไทยและลองเพิ่ม component แบบง่าย ๆ

---

## Lab 2 – ติดตั้งและแสดงผลตาราง AG Grid เบื้องต้น

### เป้าหมาย
- ติดตั้งไลบรารี AG Grid สำหรับ React
- แสดงผลตารางข้อมูลขั้นพื้นฐานในหน้า Next.js

### ขั้นตอน
1. ติดตั้งแพ็กเกจ
   ```bash
   npm install ag-grid-community ag-grid-react
   ```
2. เปิดไฟล์ `app/page.tsx` แล้วปรับให้มี Server Component สำหรับดึงข้อมูลเบื้องต้น และ Client Component สำหรับตาราง
3. สร้างไฟล์ `app/components/SalesGrid.tsx` (Client Component) ตัวอย่างโค้ด:
   ```tsx
   "use client";

   import { AgGridReact } from "ag-grid-react";
   import { ColDef, GridOptions } from "ag-grid-community";
   import { useMemo, useState } from "react";
   import "ag-grid-community/styles/ag-grid.css";
   import "ag-grid-community/styles/ag-theme-quartz.css";

   type SaleRecord = {
     id: number;
     product: string;
     region: string;
     qty: number;
     revenue: number;
   };

   const columnDefs: ColDef<SaleRecord>[] = [
     { field: "id", headerName: "รหัส", maxWidth: 100 },
     { field: "product", headerName: "สินค้า" },
     { field: "region", headerName: "ภูมิภาค" },
     { field: "qty", headerName: "จำนวน", type: "numericColumn" },
     { field: "revenue", headerName: "รายได้", type: "numericColumn" },
   ];

   const defaultColDef: ColDef = {
     sortable: true,
     resizable: true,
     filter: true,
   };

   export default function SalesGrid({ rowData }: { rowData: SaleRecord[] }) {
     const gridOptions = useMemo<GridOptions<SaleRecord>>(
       () => ({
         columnDefs,
         defaultColDef,
       }),
       []
     );

     return (
       <div className="ag-theme-quartz" style={{ height: 500 }}>
         <AgGridReact<SaleRecord> rowData={rowData} gridOptions={gridOptions} />
       </div>
     );
   }
   ```
4. ใน `app/page.tsx` ให้เตรียมข้อมูลจำลองและส่งไปยัง component
   ```tsx
   import SalesGrid from "./components/SalesGrid";

   const mockData = [
     { id: 1, product: "โน้ตบุ๊ก", region: "กรุงเทพฯ", qty: 12, revenue: 240000 },
     { id: 2, product: "เมาส์", region: "เชียงใหม่", qty: 50, revenue: 50000 },
     // ... เพิ่มข้อมูล
   ];

   export default function Page() {
     return (
       <main className="p-6 space-y-4">
         <h1 className="text-2xl font-semibold">ภาพรวมยอดขาย</h1>
         <SalesGrid rowData={mockData} />
       </main>
     );
   }
   ```
5. บันทึกไฟล์แล้วดูผลลัพธ์ในเบราว์เซอร์

### แบบฝึกหัดเพิ่มเติม
- ลองเปลี่ยนธีมเป็น `ag-theme-alpine`
- เพิ่ม column type ที่กำหนดเอง เช่น การแสดงผลตัวเลขพร้อมหน่วย

---

## Lab 3 – ปรับแต่งคอลัมน์และสไตล์

### เป้าหมาย
- เรียนรู้การใช้ `cellRenderer`, `valueFormatter`, และ `cellClass`
- รวม Tailwind CSS เพื่อจัด layout ให้สวยงาม

### ขั้นตอน
1. สร้าง `components/renderers/RevenueCell.tsx`
   ```tsx
   "use client";

   import type { ICellRendererParams } from "ag-grid-community";

   export default function RevenueCell({ value }: ICellRendererParams<number>) {
     if (value == null) return <span className="text-gray-400">-</span>;
     const formatted = value.toLocaleString("th-TH", {
       style: "currency",
       currency: "THB",
     });
     return <span className="font-medium text-emerald-600">{formatted}</span>;
   }
   ```
2. ใน `SalesGrid` เพิ่ม `frameworkComponents` และอัปเดต `columnDefs`
   ```tsx
   const columnDefs = useMemo<ColDef<SaleRecord>[]>(
     () => [
       { field: "id", headerName: "รหัส", maxWidth: 100 },
       { field: "product", headerName: "สินค้า", flex: 1 },
       { field: "region", headerName: "ภูมิภาค", flex: 1 },
       {
         field: "qty",
         headerName: "จำนวน",
         type: "numericColumn",
         valueFormatter: ({ value }) => value?.toLocaleString("th-TH"),
       },
       {
         field: "revenue",
         headerName: "รายได้",
         cellRenderer: "RevenueCell",
         cellClass: "text-right",
       },
     ],
     []
   );

   const frameworkComponents = useMemo(
     () => ({
       RevenueCell,
     }),
     []
   );
   ```
3. ปรับ `AgGridReact` ให้ส่ง `frameworkComponents`
4. เพิ่ม Tailwind class ที่ `app/globals.css` หรือ layout ให้สวยงาม เช่น กล่องการ์ด, เงา, ระยะขอบ

### แบบฝึกหัดเพิ่มเติม
- ใช้ `rowClassRules` เพื่อเปลี่ยนสีพื้นหลังแถวที่ยอดขายต่ำ
- สร้าง Tooltip ที่ดึงข้อมูลจากคอลัมน์อื่น

---

## Lab 4 – ฟีเจอร์เชิงโต้ตอบ

### เป้าหมาย
- เปิดใช้งานฟีเจอร์ Sorting, Filtering, Pagination, Selection และ Quick Filter
- เรียนรู้การใช้ `api` และ `columnApi`

### ขั้นตอน
1. เพิ่ม `pagination={true}` และ `paginationPageSize={10}` ใน `AgGridReact`
2. เปิด `rowSelection="multiple"` และสร้างปุ่มแสดงจำนวนแถวที่เลือก
3. ใช้ `onGridReady` เพื่อเก็บ reference ของ `GridApi`
   ```tsx
   const [gridApi, setGridApi] = useState<GridApi<SaleRecord> | null>(null);

   const onGridReady = useCallback((params: GridReadyEvent<SaleRecord>) => {
     setGridApi(params.api);
   }, []);
   ```
4. สร้างช่องค้นหา (Quick Filter)
   ```tsx
   <input
     type="search"
     placeholder="ค้นหา..."
     onChange={(e) => gridApi?.setQuickFilter(e.target.value)}
     className="input input-bordered"
   />
   ```
5. เพิ่มปุ่มสำหรับ export CSV `gridApi?.exportDataAsCsv()`

### แบบฝึกหัดเพิ่มเติม
- กำหนด `floatingFilter={true}`
- ลองใช้ `setColumnDefs` แบบไดนามิกเพื่อสลับมุมมอง

---

## Lab 5 – การทำงานฝั่งเซิร์ฟเวอร์

### เป้าหมาย
- ใช้ Next.js API Route (App Router) เพื่อให้บริการข้อมูลแบบ JSON
- ผสานกับ `fetch` บนฝั่งเซิร์ฟเวอร์และ `async/await`

### ขั้นตอน
1. สร้างไฟล์ `app/api/sales/route.ts`
   ```ts
   import { NextResponse } from "next/server";

   const data = [
     { id: 1, product: "โน้ตบุ๊ก", region: "กรุงเทพฯ", qty: 12, revenue: 240000 },
     // ... ดึงจากฐานข้อมูลจริงในกรณี production
   ];

   export async function GET() {
     return NextResponse.json({ rows: data });
   }
   ```
2. ใน `app/page.tsx` ใช้ `fetch` จาก API ภายใน (Server Component)
   ```tsx
   export default async function Page() {
     const res = await fetch("http://localhost:3000/api/sales", { cache: "no-store" });
     const { rows } = await res.json();

     return (
       <main>
         <SalesGrid rowData={rows} />
       </main>
     );
   }
   ```
3. เพิ่มสถานะ Loading ด้วยการสร้าง `app/loading.tsx`
4. หากต้องการใช้ฐานข้อมูลจริง ให้เชื่อมต่อกับ Prisma + PlanetScale หรือ PostgreSQL ผ่าน Prisma Client ใน API Route

### แบบฝึกหัดเพิ่มเติม
- เพิ่มการรองรับ POST เพื่อเพิ่มข้อมูลแถวใหม่
- ใช้ `revalidatePath` และ `cache: "no-cache"` สำหรับกรณี real-time

---

## Lab 6 – ข้อมูลจำนวนมากและ Virtualization

### เป้าหมาย
- ใช้ `Server-Side Row Model` หรือ `Infinite Row Model`
- จำลองการโหลดข้อมูล 10,000+ แถวอย่างมีประสิทธิภาพ

### ขั้นตอน (ตัวอย่าง Infinite Row Model)
1. ติดตั้งแพ็กเกจเสริม (หากใช้ Enterprise ให้จัดการสิทธิ์การใช้งานตามเงื่อนไข)
2. กำหนด `rowModelType="infinite"` และ `cacheBlockSize`
3. สร้าง `dataSource`
   ```tsx
   const dataSource: IDatasource = {
     getRows: async (params) => {
       const query = new URLSearchParams({
         start: params.startRow.toString(),
         end: params.endRow.toString(),
       });
       const res = await fetch(`/api/sales?${query}`);
       const { rows, total } = await res.json();

       params.successCallback(rows, total);
     },
   };
   ```
4. ใน API Route รองรับพารามิเตอร์ `start`/`end` และดึงข้อมูลตามช่วง
5. ทดสอบ performance ด้วยข้อมูลจำลองจำนวนมาก (สามารถใช้ `faker` สร้างข้อมูล)

### แบบฝึกหัดเพิ่มเติม
- ปรับค่า `maxBlocksInCache`
- ทดลองใช้ `Server-Side Row Model` พร้อม GraphQL หรือ REST API จริง

---

## Lab 7 – Integration ระดับสูง

### เป้าหมาย
- ผสานการจัดการสถานะ เช่น Zustand หรือ Redux เพื่อเก็บ filter/sort
- ใช้ AG Grid Enterprise ฟีเจอร์ เช่น Range Selection, Charts, Master/Detail (หากมีสิทธิ์ใช้งาน)
- ปรับปรุง Accessibility และ Internationalization (i18n)

### แนวทาง
1. **State Management**: เก็บค่าตัวกรองใน Zustand แล้ว sync กับ `gridApi.getFilterModel()`
2. **Custom Toolbar**: สร้าง component แยกที่ใช้ `gridApi` เรียกฟังก์ชัน export, toggle columns, density
3. **Charts**: เปิดใช้งาน `enableCharts={true}` และทดลองสร้างกราฟจากข้อมูลที่เลือก
4. **Master / Detail**: กำหนด `masterDetail` และสร้าง `detailCellRenderer` สำหรับแสดงข้อมูลใบสั่งซื้อ
5. **Accessibility**: ตรวจสอบการใช้ `aria-label`, `tabIndex`, และเปิดโหมด High Contrast
6. **i18n**: ใช้ `localeText` เพื่อแปลคำใน Grid เป็นภาษาไทยทั้งหมด

### แบบฝึกหัดเพิ่มเติม
- จัดทำระบบบันทึกการตั้งค่า grid ลง localStorage
- ผสานกับ NextAuth เพื่อตรวจสอบสิทธิ์ผู้ใช้งานก่อนเข้าถึง grid

---

## Lab 8 – ทดสอบและ Deploy

### เป้าหมาย
- เขียนการทดสอบด้วย Playwright หรือ Cypress สำหรับตาราง
- ตรวจสอบ Performance ด้วย Lighthouse และปรับปรุงหากจำเป็น
- Deploy ไปยัง Vercel พร้อมตั้งค่า Environment Variables

### ขั้นตอน
1. ติดตั้ง Playwright
   ```bash
   npx playwright install --with-deps
   ```
2. สร้างเทสต์ที่ตรวจสอบว่า grid โหลดข้อมูลและสามารถกรองได้
3. ใช้ `npm run lint` และ `npm run test` เพื่อให้แน่ใจว่าโค้ดผ่านมาตรฐาน
4. Deploy ไปยัง Vercel ด้วย `vercel` CLI หรือผ่าน GitHub Integration
5. ตั้งค่า Environment Variables เช่น API URL, DATABASE_URL ผ่านหน้า Dashboard ของ Vercel

### แบบฝึกหัดเพิ่มเติม
- ตั้งค่า CI/CD ด้วย GitHub Actions ที่รัน lint/test อัตโนมัติ
- ใช้ Vercel Preview Comments เพื่อตรวจสอบ UI ก่อน Merge

---

## แหล่งข้อมูลอ้างอิงเพิ่มเติม
- [AG Grid Documentation](https://www.ag-grid.com/react-data-grid/getting-started/)
- [Next.js Documentation](https://nextjs.org/docs)
- [AG Grid Example Repository](https://github.com/ag-grid/ag-grid)
- [Next.js + AG Grid Community Discussion](https://stackoverflow.com/questions/tagged/ag-grid+next.js)

---

## เคล็ดลับการต่อยอดหลังจบ Labs
- สร้าง Design System ของคุณเองและห่อ AG Grid ด้วย component เฉพาะองค์กร
- ผสานกับระบบ Analytics เพื่อส่ง event เมื่อผู้ใช้ filter/sort ข้อมูล
- สร้าง Storybook สำหรับแสดงสถานะต่าง ๆ ของตารางและ cell renderer
- สำรวจการใช้ Web Worker หรือ WASM เพื่อประมวลผลข้อมูลจำนวนมากก่อนส่งให้ grid

ขอให้สนุกกับการเรียนรู้ AG Grid ร่วมกับ Next.js และสามารถนำความรู้ไปประยุกต์ใช้ในโครงการจริงได้อย่างมั่นใจ!
