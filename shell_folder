using System;
using System.IO;
using System.Runtime.InteropServices;

class Program
{
    // Define a delegate for the assembly function
    private delegate void AssemblyFunction();

    static void Main(string[] args)
    {
        // Specify the directory you want to check
        string directoryPath = @"C:\SystemFolder"; // Change to your directory path

        try
        {
            // Get all directories in the specified path
            string[] directories = Directory.GetDirectories(directoryPath, "*", SearchOption.AllDirectories);
            
            foreach (var dir in directories)
            {
                // Get directory info
                DirectoryInfo dirInfo = new DirectoryInfo(dir);
                
                // Get the attributes of the folder
                FileAttributes attributes = dirInfo.Attributes;

                // Display folder path and its attributes
                Console.WriteLine($"Folder: {dir}");
                Console.WriteLine($"Attributes: {attributes}");

                // Check if it's a system folder
                if ((attributes & FileAttributes.System) == FileAttributes.System)
                {
                    Console.WriteLine(" - System flag is set. Executing assembly code...");

                    // Run the assembly code
                    ExecuteAssemblyCode();
                }

                Console.WriteLine();
            }
        }
        catch (UnauthorizedAccessException ex)
        {
            Console.WriteLine($"Access denied to directory: {ex.Message}");
        }
        catch (DirectoryNotFoundException ex)
        {
            Console.WriteLine($"Directory not found: {ex.Message}");
        }
        catch (Exception ex)
        {
            Console.WriteLine($"An error occurred: {ex.Message}");
        }
    }

    private static void ExecuteAssemblyCode()
    {
        // Define simple assembly code in bytes
        byte[] assemblyCode = new byte[]
        {
            0xB8, 0x01, 0x00, 0x00, 0x00,   // mov eax, 1
            0xC3                            // ret
        };

        // Allocate memory for the assembly code
        IntPtr buffer = Marshal.AllocHGlobal(assemblyCode.Length);

        try
        {
            // Copy the assembly code to the allocated memory
            Marshal.Copy(assemblyCode, 0, buffer, assemblyCode.Length);

            // Change the memory protection to executable
            VirtualProtect(buffer, (uint)assemblyCode.Length, 0x40, out _); // PAGE_EXECUTE_READWRITE

            // Create a delegate pointing to the memory address
            AssemblyFunction func = Marshal.GetDelegateForFunctionPointer<AssemblyFunction>(buffer);
            
            // Execute the function
            func();
        }
        finally
        {
            // Free allocated memory
            Marshal.FreeHGlobal(buffer);
        }
    }

    // Importing VirtualProtect function from kernel32.dll
    [DllImport("kernel32.dll")]
    [return: MarshalAs(UnmanagedType.Bool)]
    static extern bool VirtualProtect(IntPtr lpAddress, uint dwSize, uint flNewProtect, out uint lpflOldProtect);
}
