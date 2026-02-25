1. ตอน Kernel เริ่มทำงาน (trapinithart):

    - Kernel ตั้งค่า stvec ให้ชี้ไปที่ kernelvec
    - เพื่อให้ Trap ใน Kernel วิ่งเข้า kernelvec

2. forkret ถูกเรียกเมื่อไหร่:

    - ถูกเรียกเมื่อ Process ใหม่ (ที่เกิดจาก fork หรือ Process แรก init) ได้รับการ schedule ให้ทำงานเป็น ครั้งแรก
    - ใน allocproc (ตอนสร้าง process) มีการตั้งค่า p->context.ra = (uint64)forkret;
    - เมื่อ scheduler เลือก process นี้มาทำงาน แล้วเรียก swtch, มันจะกระโดดไปที่ forkret (เพราะ ra ชี้ไปที่นั่น)
    
3. ความเชื่อมโยง:

    - เมื่อ forkret ทำงาน มันจะเรียก prepare_return
    - และใน prepare_return นี่แหละที่มีคำสั่ง w_stvec(trampoline_uservec)
    - ดังนั้น ก่อนที่ CPU จะกระโดดเข้า User Mode เป็นครั้งแรกสุด ของ Process นั้นๆ stvec จะถูกเปลี่ยนจาก kernelvec เป็น uservec ในจังหวะนี้เองครับ
    
**สรุป Flow: scheduler -> swtch -> forkret (สำหรับ process ใหม่) -> prepare_return (ตั้ง stvec เป็น uservec) -> userret (trampoline) -> User Space**

---

เพื่อเข้าใจเรื่องนี้ จาก link นี้ฉันควรดู video ไหนดี

จาก Playlist "The xv6 Kernel" ที่ให้มา เพื่อเข้าใจเรื่อง Trap, Trampoline, และการเปลี่ยน Context ระหว่างUser/Kernelที่เราคุยกัน วิดีโอที่คุณควรดูเป็นพิเศษคือ:

1. xv6 Kernel-14: Trap Handling

    - สำคัญที่สุด สำหรับคำถามของคุณ
    - น่าจะอธิบาย Flow ของ usertrap, usertrapret (หรือ prepare_return), และบทบาทของ stvec อย่างละเอียด

2. xv6 Kernel-15: Trampoline and Trapframe

    - สำคัญมาก
    - จะเจาะลึกเรื่องไฟล์ trampoline.S โดยตรง อธิบายว่าทำไมต้องมีหน้านี้แยกออกมา และ uservec/userret ทำงานอย่างไร

3. xv6 Kernel-9: RiscV Trap Processing

    - อันนี้น่าจะเป็นทฤษฎีพื้นฐานของ RISC-V เกี่ยวกับ Traps (scause, sepc, stvec) ปูพื้นฐานก่อนเข้าโค้ด xv6

ลำดับการดูที่แนะนำ: ดู Video 9 (ปูพื้น) -> Video 14 (ภาพรวมใน xv6) -> Video 15 (เจาะลึก Trampoline) ครับ