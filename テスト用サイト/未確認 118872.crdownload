/**
 * 防災情報センター — Service Worker (sw.js)
 *
 * 役割: バックグラウンドで push 通知を受け取り、通知を表示する。
 *       通知をタップしたらサイトを開く。
 *
 * 置き場所: サイトと同じ階層のルートに置く（例: https://bousai0111.info/sw.js）
 *           ※サブフォルダに置くと通知が受け取れる範囲が狭まるので必ずルートへ。
 */

// 通知に表示する内容（固定）
const SITE_URL = 'https://bousai0111.info/';
const ICON_URL = 'https://bousai0111.info/icon-192.png'; // 無ければ後述の注記参照
const DEFAULT_TITLE = '防災情報センター';
const DEFAULT_BODY  = '今日のスタンプがまだです。1日1回タップして防災習慣を続けましょう。';

// push 受信
self.addEventListener('push', function(event){
  let data = {};
  try { if (event.data) data = event.data.json(); } catch(e){ data = {}; }

  const title = data.title || DEFAULT_TITLE;
  const body  = data.body  || DEFAULT_BODY;
  const url   = data.url   || SITE_URL;

  const options = {
    body: body,
    icon: ICON_URL,
    badge: ICON_URL,
    vibrate: [120, 60, 120],
    tag: 'dwa-stamp-reminder',   // 同じタグは上書き（通知が積み重ならない）
    renotify: true,
    data: { url: url },
    requireInteraction: false
  };

  event.waitUntil(
    self.registration.showNotification(title, options)
      .catch(function(err){ console.error('[sw] showNotification', err); })
  );
});

// 通知タップ → サイトを開く（既に開いていればそれをフォーカス）
self.addEventListener('notificationclick', function(event){
  event.notification.close();
  const target = (event.notification.data && event.notification.data.url) || SITE_URL;

  event.waitUntil(
    clients.matchAll({ type:'window', includeUncontrolled:true }).then(function(list){
      for (const c of list) {
        // 同じサイトのタブが既にあればフォーカス
        if (c.url && c.url.indexOf(self.registration.scope) === 0 && 'focus' in c) {
          return c.focus();
        }
      }
      if (clients.openWindow) return clients.openWindow(target);
    })
  );
});

// インストール即時有効化
self.addEventListener('install', function(){ self.skipWaiting(); });
self.addEventListener('activate', function(event){ event.waitUntil(self.clients.claim()); });
