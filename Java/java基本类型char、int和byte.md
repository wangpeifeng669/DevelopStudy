java基本类型char、int和byte
===
###（一）char、int和byte对比
| 类型       | 字节数     | 有无符号位  | 表示范围  |
| :---------:|:---------:| :---------:| :---------:|
| char    |  2 |  无  |  0-65535  |
| int     |  4 |  有  |  -2147483648—2147483647  |
| byte    |  1  |  有  |  -128—127  |

###（二）负数表示
有符号表示涉及原码、反码、补码，补码=反码+1。  
有符号用首位表示，1表示负数，0表示正数。  

以byte表示负数-5为例：  
1. 先将-5的绝对值转换成二进制，即为0000 0101；  
2. 然后求该二进制的反码，即为 1111 1010；  
3. 最后将反码加1，即为：1111 1011  

###（三）int和byte相互转换
####（1）int->byte
直接强制转换：byte b = (byte) aInt;  
直接截取int中最低一个字节，如果int大于255，则值就会丢失数据。
####（2）byte->int
直接转换：int i = (int) aByte;  
保持数值不变，如aByte=-5，则i也为-5。  
aByte表示为二进制为1111 1011，  
i表示为二进制为1111 1111 1111 1111 1111 1111 1111 1011  

采用位操作：int i = b & 0xff;  
保持最低字节中各个位不变，3个高字节全部用0填充，常用于编解码操作。  
如将byte数组显示成十六进制  

	public class UTF8EncodeDemo {
		private static final char[] hexArray = "0123456789ABCDEF".toCharArray();
	
		/**
		 * @param args
		 */
		public static void main(String[] args) {
			String text = "I am 君山";
			System.out.println(toHexChar(text.toCharArray()));
			System.out.println(toHexByte(text.getBytes()));
		}
	
		private static String toHexChar(char[] charArray){
			StringBuilder sb = new StringBuilder();
			for (int i = 0; i < charArray.length; i++) {
				sb.append(Integer.toHexString(charArray[i]));
				sb.append(" ");
			}
			return sb.toString();
		}
		
		private static String toHexByte(byte[] byteArray){
			char[] hexChars = new char[byteArray.length * 2];
		    for ( int j = 0; j < byteArray.length; j++ ) {
		        int v = byteArray[j] & 0xFF;
		        hexChars[j * 2] = hexArray[v >>> 4];
		        hexChars[j * 2 + 1] = hexArray[v & 0x0F];
		    }
		    return new String(hexChars);
		}
	}

	
	输出：
	49 20 61 6d 20 541b 5c71 
    4920616D20E5909BE5B1B1

**tips：**   

1. char 内部采用 unicode 16位编码，汉字"君"unicode 为\u541b。  
2. 我的 Eclipse 编译环境默认采用utf-8，所以默认text.getBytes()采用 utf-8编码格式显示，汉字在 utf-8中表示为3个字节，ascii 码表示1个字节。如果我修改文件编码格式为GBK，重新输入汉字"君山"再运行，输出为4920616D20BEFDC9BD，变成 GBK 格式，GBK 汉字只有两位。但是 char 的类型始终是固定 unicode 的。 

###（三）char和int
char字符常量2个字节，表示范围为'\u0000'-'\uFFFF'，共65536个字符，只能表示unicode中的BMP部分，其中前256个字符同 ASCII 码。  
int整型是4个字节，可以表示完整的unicode'\u0000'-'\u10FFFF'。  