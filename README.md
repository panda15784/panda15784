exp5

clc;
clear all;
close all;
%%setup and different variables 
f0=10;
fs=100;
Ts=1/fs;
N0=100;
N=2^16;
t=0:Ts:Ts*(N0-1);
x=20*sin(2*pi*f0*t);
 
figure(1)
plot (t,x);
title ('sine signal');
xlabel ('time');
ylabel ('amplitude');
grid on ;
time_lag=((-length(x)+1):1:(length(x)-1))*Ts;
 
auto_cor=xcorr(x,x)/fs;
y=1/fs*fft(auto_cor,N);
PSD2=abs(1/(N0-1)*fftshift(fft(auto_cor,N)));
f_vec = (0:1:N-1)*fs/N-fs/2;
 
figure(2)
plot(f_vec,10*log10(PSD2));
xlabel('frequency [hz]','fontsize',14)
ylabel('db/hz','Fontsize',14)
title('power spectral density-method 2','fontsize',14)
grid on;
set(gcf,'color','w');
axis tight;
 
 
figure(3)
plot(time_lag,auto_cor);
title('auto~corelation function');
xlabel('lags');
ylabel('correlation')
grid on;
 
Average_power_method_2=sum(PSD2)*fs/N;
 
noise_PSD=.5;
variance=noise_PSD*fs;
sigma=sqrt(variance);
noise=transpose(sigma*randn(N0,1));
xsignal=20*sin(2*pi*f0*t);
x=xsignal+noise;
 
figure(4)
histogram(noise,20)
set(gca,'fontsize',14)
xlabel('noise amplitude','fontsize',14)
ylabel('frequency of occurence','Fontsize',14)
title('simulated histogram of white gaussian noise','fontsize',14)
 
figure(5)
plot(t,x)
set(gca,'fontsize',14)
xlabel('time(s','fontsize',14)
ylabel('amplitude','Fontsize',14)
title(',','fontsize',14)
grid on



exp6

clc;
clear all;
close all;

nr_data_bits=8192;

b_data=(randn(1,nr_data_bits))>.5;
b=b_data;

d=zeros(1,length(b));
for n=1:length(b)
    if(b(n)==0)
        d(n)=1;
        d(n)=exp(1i*2*pi);
    end 
    if(b(n)==1)
        d(n)=exp(1i*pi);
    end
end
bpsk=d;
figure(1);
plot(d,'o');
axis([-2 2 -2 2]);
grid on;
xlabel('real');
ylabel('imag');
title('BPASK CONSTELLATION');

SNR=0:24;
BER1=[];
SNR1=[];

for SNR=0:length(SNR)
    sigma=sqrt(10.0^(-SNR/10.0));
    snbpsk=(real(bpsk)+sigma.*randn(size(bpsk)))+1i.*(imag(bpsk)+sigma*randn(size(bpsk)));
    
    figure(2);
    plot(snbpsk,'o');
    axis([-2 2 -2 2]);
    grid on;
    xlabel('real');
    ylabel('imag');
    title('BPSK constellation with noise');
    
    %receiver
    r=snbpsk;

    ptr=real(r)<0;
    ptr=ptr(:)';
    ptr1=ptr;

    ne=sum(b~=ptr1);
    BER=ne/nr_data_bits;
    BER1=[BER1 BER];
    SNR1=[SNR1 SNR];
end
figure(3);
semilogy(SNR1,BER1,'-*');
grid on;
xlabel('SNR=Eb/No(db)');
ylabel('BER')
title('simulation of BER/SNR for BPSK');
legend('BER-simulated');



exp7


clc;
close all;
% Generation of random Bits
r = round(rand(1, 10));
disp(r)

% Chip pattern for station A, B, and C
a_one = [1 -1 -1 1 -1 1];
a_zero = -1 * a_one;
b_one = [1 1 -1 -1 1 1];
b_zero = -1 * b_one;
c_one = [1 1 -1 1 1 -1];
c_zero = -1 * c_one;

% Random allotment of bits to stations A, B, and C
cdma_seq = [];
for counter = 1:10
    switch (randi(3, 1, 1))
        case 1
            if r(1, counter) == 0
                cdma_seq = [cdma_seq a_zero];
            else
                cdma_seq = [cdma_seq a_one];
            end
        case 2
            if r(1, counter) == 0
                cdma_seq = [cdma_seq b_zero];
            else
                cdma_seq = [cdma_seq b_one];
            end
        case 3
            if r(1, counter) == 0
                cdma_seq = [cdma_seq c_zero];
            else
                cdma_seq = [cdma_seq c_one];
            end
    end
end

cntr = 0;
for selector = 1:6:60
    cntr = cntr + 1;
    temp = [cdma_seq(1, selector) cdma_seq(1, selector + 1) cdma_seq(1, selector + 2) cdma_seq(1, selector + 3) cdma_seq(1, selector + 4) cdma_seq(1, selector + 5)];
    
    result1 = dot(a_one, temp);
    result2 = dot(b_one, temp);
    result3 = dot(c_one, temp);
    
    if (result1 == 6 || result1 == -6)
        fprintf('The bit # %d is from Station A\n', cntr);
    elseif (result2 == 6 || result2 == -6)
        fprintf('The bit # %d is from Station B\n', cntr);
    elseif (result3 == 6 || result3 == -6)
        fprintf('The bit # %d is from Station C\n', cntr);
    end
end




exp8


clc;
clear all;
close all;
m = input ('ENTER THE NUMBER OF SYMBOLS : ');
p = input('ENTER THE PROBABITY OF SYMBOLS : ');
disp('PROBABILITIES OF MESSAGE :');
for i = 1:m
    for j=i+1:m
        if(p(i)<p(j))
            temp = p(i);
            p(i)=p(j);
            p(j)=temp;
        end
    end
end
 
disp(p);
n=1:m;
[dict,avglen]=huffmandict(n,p);
disp('AVERAGE LENGTH OF CODE : ')
disp(avglen);
temp=dict;
for i=1:length(temp)
    temp{i,2}=num2str(dict{i,2});
end
disp('HUFFMAN CODDED DICTIONARY :');
disp(temp);
 
input_sig=input('ENTER INPUT SIGNAL : ' );
dsig=huffmanenco(input_sig,dict);
disp('ENCODEDED SIGNAL : ');
disp(dsig);
r = input('ENTER RECEIVED SIGNAL : ');
output_sig= huffmandeco(r,dict);
disp('DECODED SIGNAL :');
 
disp(output_sig);
z= isequal(input_sig,output_sig);
if(z==1)
    disp('RECEIVED DATA IS CORRECT');
else
    disp('RECEIVED DATA IS INCORRECT');
end
Hx=0;
for i=1:m
    Hx=Hx + ( p(i) * log2(1/p(i)));
end
disp('ENTROPY IS : ');
disp(Hx);
 
disp('EFFICIENCY IS PERCENTAGE ');
eff=Hx/avglen*10
disp(eff);






exp9

clc;
clear all;
close all;

i=input('Enter no. of element : ');
p=input('Enter no. of probability : ');
q=input('Enter no. of conditional matrix : ');
sum=0;
for n = 1: i
    H=sum + p(n)* log(1/p(n))/log(2);
    sum=H;
end 
disp('H(x) : ');
disp(H);
for n=1:i
    for m=1:i
       a(n,m)=q(n,m)*p(n);
    end 
end
disp('P(x,y):');
disp(a);
d=0;
for n=1:i
    for m=1:i
        H1=d+(a(n,m)*log2(1/(q(n,m))));
        d=H1;
    end 
end
disp('H(y/x) : ');
disp(H1);
for n=1:i
    w=0; 
    for m=1:i
    s(n)=w+a(m,n);
    w=s(n);
    end 
end
disp('P(y):');
disp(s);
k=0;
for n=1:i
    H2=k+(s(n)*log2(1/s(n)));
    k=H2;
end
disp('H(y):');
disp(H2);
m=H2-H1;
disp('mutual information : ');
disp(m);



exp10

clc; 
n=input('enter value of n'); 
k=input('enter value of k'); 
p=input('parity matrtix'); 
i=eye(k);
 
disp ('identity matrix:'); 
disp(i); 
q=n-k; 
disp('total no of check bits:'); 
disp(q); 
x=0:1:2^k-1; 
m=[de2bi(x,k,'left-msb')]; 
disp('message vectors :'); 
disp(m);

 g=[i p]; 
disp('gemerator matrix'); d
isp (g);
 s=m*g; 
s1=rem(s,2); 
disp('codewords:'); 
disp(s1); 

r=input('enter the recieved code words:'); 
pt=transpose(p); 
b=eye(q); 
h=[pt b]; 
disp('parity check matrix:'); 
disp(h);
 ht=transpose(h); 
disp('transpose of parity check matrix:'); 
disp(ht); 
d=(r*ht); 

s2=rem(d,2); disp('syndrome vector:'); 
disp(s2); 
e=eye(n); 
disp('error matrix:'); 
disp(e); 
if (s2==0) 
disp('codeword is correct'); 
else 
for i=1:n 
if(s2==ht(i,:)) 
c=xor(r,e(i,:)); 
disp('corrected codeword'); 
disp (c); 
end 
end
end
