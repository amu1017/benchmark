Add-Type -TypeDefinition @"
    using System;
    using System.Runtime.InteropServices;
    public class MouseJiggler {
        [DllImport("user32.dll", CharSet = CharSet.Auto, CallingConvention = CallingConvention.StdCall)]
        public static extern void mouse_event(long dwFlags, long dx, long dy, long cButtons, long dwExtraInfo);
        public const long MOUSEEVENTF_MOVE = 0x0001;
        private static Random random = new Random();

        public static void Jiggle() {
            int x = 50, y = 50; // 開始点を中央(50, 50)に設定
            for (int i = 0; i < 50; i++) { // 約5秒間、0.1秒ごとに移動
                int dx = random.Next(-10, 11); // -10〜10のランダムな移動量
                int dy = random.Next(-10, 11);

                x += dx;
                y += dy;

                // 100x100の範囲内に制限
                x = Math.Max(0, Math.Min(100, x));
                y = Math.Max(0, Math.Min(100, y));

                // 現在地からの相対移動量を計算
                mouse_event(MOUSEEVENTF_MOVE, dx, dy, 0, 0);
                System.Threading.Thread.Sleep(1); // 0.001秒スリープ
            }
        }
    }
"@

# 初回だけメッセージを表示
if (-not $script:displayedMessage) {
    Write-Host "Mouse Jiggler is running... Press Ctrl+C to stop."
    $script:displayedMessage = $true
}

# 無限ループでマウスを動かす
while ($true) {
    [MouseJiggler]::Jiggle()
    Start-Sleep -Seconds 3
}
