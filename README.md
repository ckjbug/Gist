# Csharp Gist

> Csharp Gist代码片段
>CSharp_Util_Library类库
>>DotNet.Utilities（整理）
---------

C#调用CMD来执行命令

'''cs
	        //要执行的指令，如关机：shutdown -s -t 00
	        string str = "xxx"；
	        //开启一个新的进程用来跑CMD
                        Process p = new Process();
                        //设置要启动的应用程序
                        p.StartInfo.FileName = "cmd.exe";
                        //是否使用操作系统shell启动
                        p.StartInfo.UseShellExecute = false;
                        // 接受来自调用程序的输入信息
                        p.StartInfo.RedirectStandardInput = true;
                        //输出信息
                        p.StartInfo.RedirectStandardOutput = true;
                        // 输出错误
                        p.StartInfo.RedirectStandardError = true;
                        //不显示程序窗口
                        p.StartInfo.CreateNoWindow = true;
                        //启动程序
                        p.Start();
                        //向cmd窗口发送输入信息
                        p.StandardInput.WriteLine(str + "&exit");
                        p.StandardInput.AutoFlush = true;
                        //获取输出信息
                        string strOuput = p.StandardOutput.ReadToEnd();
                        //等待程序执行完退出进程
                        p.WaitForExit();
                        p.Close();		
'''                      

-----------------------

C#实现程序(x.exe)的重启

'''cs
Application.Exit();
System.Diagnostics.Process.Start(System.Reflection.Assembly.GetExecutingAssembly().Location);	
//或者使用Application.Restart();
'''

-----------------------

C#实现程序开机自动启动（通过注册表设置启动项）
using system .microsoft.win32;//一定要引用
RegistryKey rk = Registry.LocalMachine;
RegistryKey rk2 = rk.CreateSubKey(@"Software\Microsoft\Windows\CurrentVersion\Run");
rk2.SetValue( "autorun",程序路径);
rk2.Close();
rk.Close();

-----------------------

C#通过调用窗口句柄进行截屏（功能更强大）


C#通过Winform程序获取Windows系统桌面截屏
//获取主屏
            
Screen s = Screen.PrimaryScreen;
  
//获取系统上所有显示器的截屏
            
//Screen[] screens = Screen.AllScreens;          
//创建一个位图,将其大小设置为何屏幕大小一致,为了获取屏幕的图片
            
Bitmap bit = new Bitmap(s.Bounds.Width, s.Bounds.Height);
            
//利用当前bit获取一个画布,画布已经于Graphics对象关联
            
using (Graphics g = Graphics.FromImage(bit))
            {
                //将屏幕的(0,0)坐标截图内容copy到画布的(0,0)位置,尺寸到校 bit.size;
                g.CopyFromScreen(new Point(0, 0), new Point(0, 0), bit.Size);
                //将位图保存到D盘
                bit.Save(@"C:\Users\Enz\Desktop\desktop.jpg");
                //释放位图资源
                bit.Dispose();
                //释放画布
                //g.Dispose();
            }

-----------------------

C#使用TcpClient/TcpListener进行异步通信操作
public static class TcpHelper
    {
        public static async void ConnectAsTcpClient()
        {
            using (var tcpClient = new TcpClient())
            {
                Console.WriteLine("[Client] Connecting to server");
                await tcpClient.ConnectAsync("127.0.0.1", 1234);
                Console.WriteLine("[Client] Connected to server");
                using (var networkStream = tcpClient.GetStream())
                {
                    Console.WriteLine("[Client] Writing request {0}", ClientRequestString);
                    await networkStream.WriteAsync(ClientRequestBytes, 0, ClientRequestBytes.Length);

                    var buffer = new byte[4096];
                    var byteCount = await networkStream.ReadAsync(buffer, 0, buffer.Length);
                    var response = Encoding.UTF8.GetString(buffer, 0, byteCount);
                    Console.WriteLine("[Client] Server response was {0}", response);
                }
            }
        }

        private static readonly string ClientRequestString = "Some HTTP request here";
        private static readonly byte[] ClientRequestBytes = Encoding.UTF8.GetBytes(ClientRequestString);

        private static readonly string ServerResponseString = "<?xml version=\"1.0\" encoding=\"utf-8\"?><document><userkey>key</userkey> <machinemode>1</machinemode><serial>0000</serial><unitname>Device</unitname><version>1</version></document>\n";
        private static readonly byte[] ServerResponseBytes = Encoding.UTF8.GetBytes(ServerResponseString);

        public static async void StartListener()
        {
            var tcpListener = TcpListener.Create(1234);
            tcpListener.Start();
            var tcpClient = await tcpListener.AcceptTcpClientAsync();
            Console.WriteLine("[Server] Client has connected");
            using (var networkStream = tcpClient.GetStream())
            {
                var buffer = new byte[4096];
                Console.WriteLine("[Server] Reading from client");
                var byteCount = await networkStream.ReadAsync(buffer, 0, buffer.Length);
                var request = Encoding.UTF8.GetString(buffer, 0, byteCount);
                Console.WriteLine("[Server] Client wrote {0}", request);
                await networkStream.WriteAsync(ServerResponseBytes, 0, ServerResponseBytes.Length);
                Console.WriteLine("[Server] Response has been written");
            }
        }
    }

static void Main(string[] args)
        {
            TcpHelper.StartListener();
            TcpHelper.ConnectAsTcpClient();
            Console.ReadLine();
        }

'''










![DotNet.Utilities](https://github.com/ckjbug/Programming-Road/blob/master/CSharp_Util_Library.png)
