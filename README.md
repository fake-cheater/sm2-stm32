# sm2-stm32
国密算法 for 单片机stm32, 静态内存分配

测试使用方法：
在有gcc的环境下，直接make
然后自动生成可执行文件 test

int sm2_test()
{
	printf("sm2 test....\n");
	
	
	unsigned char dB[] = { 0x16,0x49,0xAB,0x77,0xA0,0x06,0x37,0xBD,0x5E,0x2E,0xFE,0x28,0x3F,0xBF,0x35,0x35,0x34,0xAA,0x7F,0x7C,0xB8,0x94,0x63,0xF2,0x08,0xDD,0xBC,0x29,0x20,0xBB,0x0D,0xA0 };
	unsigned char xB[] = { 0x43,0x5B,0x39,0xCC,0xA8,0xF3,0xB5,0x08,0xC1,0x48,0x8A,0xFC,0x67,0xBE,0x49,0x1A,0x0F,0x7B,0xA0,0x7E,0x58,0x1A,0x0E,0x48,0x49,0xA5,0xCF,0x70,0x62,0x8A,0x7E,0x0A };
	unsigned char yB[] = { 0x75,0xDD,0xBA,0x78,0xF1,0x5F,0xEE,0xCB,0x4C,0x78,0x95,0xE2,0xC1,0xCD,0xF5,0xFE,0x01,0xDE,0xBB,0x2C,0xDB,0xAD,0xF4,0x53,0x99,0xCC,0xF7,0x7B,0xBA,0x07,0x6A,0x42 };
	unsigned char tx[257];
	unsigned char etx[256];
	unsigned char mtx[256];
	//FILE *fp=0;
	int wxlen, wylen, privkeylen,len;
	//fopen(&fp, "5.txt", "r");
	//len=fread_s(tx, 256,sizeof(unsigned char), 256, fp);
	//fp = fopen("5.txt","r");
	//len=fread(tx,1,256,fp);
		tx[0] = 0x31;
		tx[1] = 0x31;
		tx[2] = 0x31;
		tx[3] = 0x31;
		len = 256;
	tx[len] = 0;
	sm2_keygen(xB, &wxlen, yB, &wylen, dB, &privkeylen);
	printf("dB: ");
	PrintBuf(dB, 32);
	printf("xB: ");
	PrintBuf(xB, 32);
	printf("yB: ");
	PrintBuf(yB, 32);
	sm2_encrypt(tx,len,xB,32,yB,32,etx);
	printf("\n``````````````````this is encrypt```````````````````\n");
	PrintBuf(etx, 64 +len + 32);
	printf("\n``````````````````this is decrypt```````````````````\n");
	sm2_decrypt(etx,64+len+32,dB,32,mtx);
	if(sm2_decrypt(etx,64+len+32,dB,32,mtx) < 0)
		printf("sm2_decrypt error!\n");
	else
	{
		PrintBuf(mtx, len);
		Printch(mtx, len);
	}

	printf("\n``````````````````this is end```````````````````````\n");
	
	unsigned char ca_publickey[] ={0x4a,0xb4,0x60,0x23,0xee,0x81,0x43,0xd0,0x89,0x0f,0x5b,0xe5,0x88,0x22,0x5e,0x17,0xcd,0x7e,0x19,0x88,0x64,0x74,0xa2,0x4a,0xa4,0xc3,0x3f,0x35,0x9c,0xb7,0xdb,0xe4,0x83,0xb4,0x72,0x95,0x48,0x4e,0x8d,0x3b,0xef,0x03,0x45,0x09,0x8b,0xd5,0x61,0x96,0xd4,0x75,0x0d,0xd4,0x40,0xf0,0x4d,0xbe,0x81,0xa0,0x9d,0x06,0x5c,0xa1,0x59,0xae};
	unsigned char ca_sign[]={0x5f,0xda,0x05,0x59,0x52,0xfe,0xdc,0xcf,0xd0,0x6d,0x46,0xad,0xe4,0xf2,0xbd,0x85,0x59,0x8c,0x12,0xba,0x7c,0xda,0x6c,0xbb,0xa9,0x5d,0x17,0xb9,0x4f,0xa1,0x72,0xdf,0x1f,0x54,0xef,0x50,0x04,0xe9,0x76,0xb4,0x34,0xb4,0x7f,0xf1,0x08,0xcc,0x91,0x22,0x0d,0xcb,0x9f,0x75,0x5c,0xbb,0xf5,0x87,0xa1,0x35,0x18,0xdf,0xc0,0x71,0xa9,0x6e};
	
	unsigned char ca_source[]={0x12,0x00,0x00,0x00,0x00,0x12,0x99,0x00,0x00,0x00,0x04,0x04,0x00,0x33,0x03,0xa0,0x80,0x98,0x56,0xe4,0xfd,0xd3,0x6d,0xba,0x5d,0x38,0x06,0x14,0xe0,0x69,0x84,0x48,0xfa,0x40,0x4d,0xc4,0x9e,0x2d,0xf3,0x70,0xb9,0x65,0x74,0xff,0xaf,0x39,0x0d};
	int ret = mt_sm2_verify(ca_publickey,sizeof(ca_publickey),ca_sign,sizeof(ca_sign),ca_source,sizeof(ca_source));
	if(ret == 1){
		 	printf("\n``````````````````this is success```````````````````````\n");
	}else
	{ 
		  printf("\n``````````````````this is failed```````````````````````\n");
  }
	
	unsigned char ca_publickey1[] ={0x46,0xd2,0x68,0x06,0xfa,0xda,0x1d,0xc0,0x0a,0xb8,0xca,0x28,0x48,0x85,0x37,0x05,0x26,0x11,0x59,0xca,0xf5,0x5c,0xe8,0xc5,0x12,0x5b,0xc1,0x71,0x77,0x05,0x39,0x2a,0xca,0xd4,0xd4,0x65,0x84,0x09,0x97,0xdc,0xde,0xc2,0x58,0x02,0x5a,0xa2,0x71,0xfe,0xec,0xf4,0xda,0x60,0xf8,0x6a,0xc2,0x3d,0x96,0x20,0xe9,0x13,0x82,0x9f,0x0e,0x4c};
	unsigned char ca_sign1[]={0xbe,0x00,0x1b,0x1b,0xf6,0x0e,0x9e,0x02,0x1e,0xba,0x5e,0x7d,0xf5,0xa5,0x2f,0x43,0xe1,0x4f,0xce,0x66,0xd9,0xb2,0x55,0xa4,0x95,0x48,0xef,0xfb,0x3c,0x2e,0x18,0xca,0x42,0xd7,0xf2,0x0b,0xb4,0xb8,0x87,0xfb,0xb0,0x44,0x67,0x3b,0x50,0x3a,0x58,0xdc,0x1f,0x85,0x99,0x04,0x9d,0x51,0xe0,0xfc,0x99,0x5e,0x40,0x55,0xbe,0x58,0x15,0x6a};
	
	unsigned char ca_source1[]={0x12,0x03,0x60,0x42,0x70,0x01,0x31,0x14,0x45,0x01,0x04,0x04,0x00,0x21,0x02,0xe1,0xcb,0x8e,0xae,0x77,0xa1,0xb5,0x88,0xca,0xb9,0x1e,0x02,0x20,0xfd,0xa2,0x0b,0x30,0x95,0x9f,0xc9,0x30,0xc9,0x67,0xd1,0xba,0x10,0x61,0x41,0xf4,0x29,0xf2,0xb6};
	ret = mt_sm2_verify(ca_publickey1,sizeof(ca_publickey1),ca_sign1,sizeof(ca_sign1),ca_source1,sizeof(ca_source1));
	if(ret == 1){
		 	printf("\n``````````````````this is success```````````````````````\n");
	}else
	{ 
		  printf("\n``````````````````this is failed```````````````````````\n");
  }
	
	
	return 0;
}
