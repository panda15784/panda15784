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



