# Phase 32: Call Center WebRTC + WhatsApp QR Integration

## 1. Database Schema
- [x] Add `SipExtension` model
- [x] Add `CallRecord` model
- [x] Add `WhatsappSession` model  
- [x] Add `WhatsappBaileysMessage` model
- [x] Add enums: `WhatsappSessionStatus`, `WaBaileysMessageDirection`, `WaBaileysMessageStatus`
- [x] Add relations to `Tenant`, `User`, `Client`, `Case`

## 2. Backend — Call Center Module
- [x] `sip-extension.service.ts` — AES encrypted SIP passwords
- [x] `call-record.service.ts` — auto-client matching, stats
- [x] `call-center.controller.ts` — REST API endpoints
- [x] `call-center.module.ts`

## 3. Backend — WhatsApp Baileys
- [x] `whatsapp-baileys.service.ts` — QR session, message handling, auto-reconnect
- [x] Update `whatsapp.controller.ts` with QR endpoints
- [x] Update `whatsapp.module.ts`
- [x] Update `websocket.gateway.ts` with `broadcastWhatsAppQR` and `broadcastWhatsAppStatus`

## 4. Backend — App Module + Dependencies
- [x] Run `npx prisma generate`
- [x] Install `@whiskeysockets/baileys`, `qrcode`, `crypto-js`
- [x] Register `CallCenterModule` in `app.module.ts`

## 5. Frontend — Softphone
- [x] `api/callCenter.ts` — SIP extension + call record API
- [x] `hooks/useSoftphone.ts` — JsSIP WebRTC hook
- [x] `components/call-center/Softphone.tsx` — floating dialer UI

## 6. Frontend — WhatsApp QR
- [x] `api/whatsappQr.ts` — connect, disconnect, status, send, messages API
- [x] `components/whatsapp/WhatsappConnect.tsx` — QR display + status
- [x] Update `hooks/use-websocket.ts` with `useWhatsAppQR` and `useWhatsAppStatus`
- [x] Update `lib/websocket.ts` with new event types
- [x] Install `jssip`, `qrcode.react`

## 7. Verification
- [x] Backend `tsc --noEmit` — ✅ zero errors
- [x] Frontend `tsc --noEmit` — ✅ zero errors
