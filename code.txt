clc;
close all;
clear all;
[filename pathname] = uigetfile({'*.jpg';'*.bmp';'*.png'},'File Selector');
myImage = strcat(pathname, filename);
f=imread(myImage);
f=imresize(f,[400 NaN]);                   %%image loading unit
figure;
imshow(f);
g=rgb2gray(f);
figure;
imshow(g);
g=medfilt2(g,[3 3]);0
figure;
imshow(g);
conc=strel('disk',1);
gi=imdilate(g,conc);
figure;
imshow(gi);
ge=imerode(g,conc);
figure;
imshow(ge);  %%%% morphological image processing
gdiff=imsubtract(gi,ge);
figure;
imshow(gdiff);
gdiff=mat2gray(gdiff);
figure;
imshow(gdiff);
gdiff=conv2(gdiff,[1 1;1 1]);
figure;
imshow(gdiff);
gdiff=imadjust(gdiff,[0.5 0.7],[0 1],.1);
figure;
imshow(gdiff);
B=logical(gdiff);
figure;
imshow(B);
[a1 b1]=size(B);
figure;
imshow(B)
er=imerode(B,strel('line', 100,0));
figure;

imshow(er)
out1=imsubtract(B,er);
figure;
imshow(out1);
F=imfill(out1,'holes');      %%%filling the object
figure;
imshow(F);
H=bwmorph(F,'thin',1);
figure;
imshow(H);
H=imerode(H,strel('line',3,90));
figure;
imshow(H)

final=bwareaopen(H,floor((a1/15)*(b1/15)));  
figure;
imshow(final);
final(1:floor(.9*a1),1:2)=1;
figure;
imshow(final);
final(a1:-1:(a1-20),b1:-1:(b1-2))=1;
figure;
imshow(final);
yyy=template(2);
figure;
imshow(final)
Iprops=regionprops(final,'BoundingBox','Image');
hold on
for n=1:size(Iprops,1)
    rectangle('Position',Iprops(n).BoundingBox,'EdgeColor','g','LineWidth',2); 
end
hold off
NR=cat(1,Iprops.BoundingBox);   %%Data storage section
[r ttb]=connn(NR);

if ~isempty(r)
    
    
    xlow=floor(min(reshape(ttb(:,1),1,[])));
    xhigh=ceil(max(reshape(ttb(:,1),1,[])));
    xadd=ceil(ttb(size(ttb,1),3));
    ylow=floor(min(reshape(ttb(:,2),1,[])));    %%%%%area selection
    yadd=ceil(max(reshape(ttb(:,4),1,[])));
    final1=H(ylow:(ylow+yadd+(floor(max(reshape(ttb(:,2),1,[])))-ylow)),xlow:(xhigh+xadd));
    [a2 b2]=size(final1);
    final1=bwareaopen(final1,floor((a2/20)*(b2/20)));
    figure(6)
    imshow(final1)
    
   
    Iprops1=regionprops(final1,'BoundingBox','Image');
    NR3=cat(1,Iprops1.BoundingBox);
    I1={Iprops1.Image};
    
    carnum=[];
    if (size(NR3,1)>size(ttb,1))
        [r2 to]=connn2(NR3);
        
        for i=1:size(Iprops1,1)
            
            ff=find(i==r2);
            if ~isempty(ff)
                N1=I1{1,i};
                letter=readLetter(N1,2);
            else
                N1=I1{1,i};
                letter=readLetter(N1,1);
            end
            if ~isempty(letter)
                carnum=[carnum letter];
            end
        end
    else
        for i=1:size(Iprops1,1)
            N1=I1{1,i};
            letter=readLetter(N1,1);
            carnum=[carnum letter];
        end
    end
    fid1 = fopen('carnum.txt', 'wt');
    fprintf(fid1,'%s',carnum);
    fclose(fid1);
    winopen('carnum.txt')
   


else
    fprintf('license plate recognition failure\n');
    fprintf('Characters are not clear \n');
end